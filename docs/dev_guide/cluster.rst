Clusters and Sessions
=====================

The |Cluster| object is the central point of configuration between an
application and the driver, and often the first driver object an application
instantiates.
A |Cluster| does several things:

- It provides an interface that the user can use to create |Sessions|
  for interacting with their Cassandra instance.
- To support this behavior, it provides :ref:`attributes <cluster-attributes>`
  for configuring |Sessions| connected to the Cassandra cluster, and
- holds the objects that maintain those connections and maintains data that
  persists between |Session| instances.

Some |Cluster| attributes are set by the user and remain static.
These options are out of scope for this document.
Instead, we'll focus on how |Sessions| and |Clusters| persist necessary
configuration between |Session| instances.
(Note that, typically, users will create a single |Session| at a time for a
given |Cluster|, only creating more if the first fails.
In fact, in our next major release, the |Cluster| abstraction will no longer be
part of the public API and users will create |Sessions| directly.)

.. code-block:: plantuml

    @startuml

    title Relationships - Class Diagram

    class Dwelling {
     +Int Windows
     +void LockTheDoor()
    }

    class ApartmentLol
    class House
    class Commune
    class Window
    class Door

    Dwelling <|-down- Apartment: Inheritance
    Dwelling <|-down- Commune: Inheritance
    Dwelling <|-down- House: Inheritance
    Dwelling "1" *-up- "many" Window: Composition
    Dwelling "1" *-up- "many" Door: Composition

    @enduml


---------------------


A |Cluster| object holds a number of registries:

- a registry of registered user types
- a registry of execution profiles
- a registry of prepared statements


- generic config
    - contact points
- auth provider
- ExecutionProfile
    - dunno what's necessary to document here
- address translator
- metrics collector
- timestamp generator
- metadata
- connections
    - control connection
    - "pool" (actually host)
- policies
    - lbp
    - reconnection policy
    - retry
    - conviction
    - hoststatelistener
- address translator
- timestamp generator
- executor/scheduler
    - run_in_executor decorator
- control connection
- registries
    - usertype registry
    - execution profile registry
    - prepared statements?
- connection factory
- shutdown
- future-handling (_on_up_future_completed)
- reconnector
- on_add/add_host example

Sessions

- row factory
- client-side timestamp generation
- ProtocolHandler
- execution
    - its own topic
    - responsefuture creation
    - paging
- request init listeners
- waiting for schema agreement

ResponseFuture

- callbacks

resultset

- paging

connections

- authenticator
- reactor
- socket init stuff
- holds requests
- request id issuer
- states
  - defunct
  - closed
- tie in with reactors?
- watchers

Types

- handle serialization and deserialization
  - which is impl'd in Cython


.. |Session| replace:: :class:`.Session`
.. |Sessions| replace:: :class:`.Session`\ s
.. |Cluster| replace:: :class:`.Cluster`
.. |Clusters| replace:: :class:`.Cluster`\ s
