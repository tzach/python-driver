Execution Path
==============

The driver uses a |ResponseFuture|, a custom future abstraction, as the
entry point for ongoing statement executions.
This is the type returned by :meth:`.Session.execute_async`.
A |ResponseFuture| does much of its work by with resources it gets from the
|Session| it holds a reference to.

.. code-block:: plantuml

    @startuml
    title ResponseFuture and Associated Objects

    skinparam rectangle {
      BorderColor Transparent
    }
    skinparam shadowing false


    left to right direction

    object ResponseFuture
    object Cluster
    object Session {
      ExecutionProfile registry
      RetryPolicy
    }
    object HostConnection {
      timers
    }
    object Reactor

    ResponseFuture -r-> Session
    Session -r-> Cluster

    Session --> HostConnection: 1 connection per host
    ResponseFuture --> HostConnection
    HostConnection -> Reactor

    @enduml


At a high level, the happy path for a statement execution -- initiated with
:meth:`.Session.execute` and managed by the |ResponseFuture| it returns --
works like this:

- The |Session| object creates a |ResponseFuture|
  (:meth:`.Session.create_response_future`), passing along any necessary
  configuration.
  The |ResponseFuture| retains a reference to this |Session|.
  The |Session| then initiates the ``ResponseFuture``\ 's first request.
- The |ResponseFuture| requests a |HostConnection| from its |Session|
  (see :meth:`ResponseFuture._query`), then asks that ``Connection`` for a lease on
  a request ID (see :meth:`.HostConnection.borrow_connection`).
  It also starts a timer (managed by the ``Connection``) to schedule speculative
  execution or a request timeout as appropriate.
- If the |ResponseFuture| is able to get a currently-unused request ID, it uses
  that connection to send a request (see :meth:`.Connection.send_msg`) with that ID
  to the database.

.. code-block:: plantuml

    @startuml

    skinparam ParticipantPadding 20
    skinparam BoxPadding 100

    participant ResponseFuture
    participant HostConnection
    participant Session

    group Issuing a Request
      Create ResponseFuture
      |||
      ResponseFuture <- Session: Create and configure
      ResponseFuture <- Session: Initiate request
      ResponseFuture -> Session: Request HostConnection
      Session -> ResponseFuture: Provide reference to HostConnection for queried Host
      ResponseFuture -> HostConnection: Request request ID
      HostConnection -> ResponseFuture: Provide available request ID
      ResponseFuture -> HostConnection: Start timer for spec exec/timeout
    end

    box #LightBlue
    participant HostConnection
    participant Session
    end box

    group Processing a Response
      [-> ResponseFuture: Application requests result
      ResponseFuture -> HostConnection: Request result
      activate HostConnection
      HostConnection -> HostConnection: Block until done
      HostConnection -> ResponseFuture: Return result
      deactivate HostConnection
    end

    @enduml


The timer and request sent to the database are then in the hands of whichever
:ref:`reactor <../reactors>` the driver is configured to use.
When the application asks the |ResponseFuture| for the result, it will wait
until either the response returns or the future's timeout expires.
In either case the request's ID is returned to the connection as a valid ID.

Response Processing
-------------------

Response processing is handled in a callback that is set on |ResponseFuture|
initialization.
This callback can do a number of things:

- On happy paths, sets the result on the |ResponseFuture| or sets metadata on
  the |Session| to reflect schema changes.
- On errors, it logs any necessary metrics, then defers to its
  :class:`.RetryPolicy` (set on initialization by the user or the |Session|) to
  determine if and how the request should be retried.


.. |ResponseFuture| replace:: :class:`.ResponseFuture`
.. |Session| replace:: :class:`.Session`
.. |Connection| replace:: :class:`.Connection`
.. |HostConnection| replace:: :class:`.HostConnection`
