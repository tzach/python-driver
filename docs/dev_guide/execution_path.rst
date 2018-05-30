Execution Path
==============

The driver uses a |ResponseFuture|, a custom future abstraction, as the
entry point for ongoing statement executions.
This is the type returned by :meth:`.Session.execute_async`.
A |ResponseFuture| does much of its work by with resources it gets from the
|Session| to which it holds a reference.

.. code-block:: plantuml

	@startuml

	skinparam rectangle {
	  BorderColor Transparent
	}
	skinparam shadowing false


	left to right direction

	object ResponseFuture
	object Cluster
	rectangle {
	  object Session
	  object HostConnection
	}

	ResponseFuture -l-* Session: has 1
	Session -r-* Cluster: has 1

	Session -- HostConnection: 1 per host
	ResponseFuture --* HostConnection: has 1

	@enduml

At a high level, the happy path for a statement execution managed by the
|ResponseFuture| works like this:

- The |Session| object :meth:`creates <.Session.create_response_future>` a
  |ResponseFuture|, passing along any necessary configuration.
  The |Session| then initiates the ``ResponseFuture``\ 's first request.
- The |ResponseFuture| :meth:`asks <ResponseFuture._query>` its |Session| for a
  |HostConnection|, then :meth:`asks <.HostConnection.borrow_connection>` that
  ``Connection`` for a lease on a request ID.
  It also starts a timer to schedule speculative execution or a request timeout
  as appropriate.
- If the |ResponseFuture| is able to get a currently-unused request ID, it uses
  that connection to :meth:`send a request <.Connection.send_msg>` with that ID
  to the database.

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

- On happy paths, it sets metadata on the |Session| to reflect schema changes or
  sets the result on the |ResponseFuture|.
- On errors, it registers any necessary metrics, then defers to its
  :class:`.RetryPolicy` (set on initialization by the user or the |Session|) to
  determine if and how the request should be retried.


.. |ResponseFuture| replace:: :class:`.ResponseFuture`
.. |Session| replace:: :class:`.Session`
.. |Connection| replace:: :class:`.Connection`
