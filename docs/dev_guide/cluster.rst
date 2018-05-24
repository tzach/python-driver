Clusters and Sessions
=====================

The :class:`.Cluster` object is often the first driver object an application
instantiates.

It provides `attributes <../api/cassandra/cluster/index.html#cassandra.cluster.Cluster-attributes>`_ for configuring the connection to the Cassandra cluster,

.. It provides `attributes <api/cassandra/cluster.html#cassandra.cluster.Cluster-attributes>`_ for configuring the connection to the Cassandra cluster,

.. It provides `attributes <../api/cassandra/cluster.html#cassandra.cluster.Cluster-attributes>`_ for configuring the connection to the Cassandra cluster,

.. It provides :doc:`attributes <../api/cassandra/cluster#cassandra.cluster.Cluster-attributes>` for configuring the connection to the Cassandra cluster,

holds the objects that maintain those connections, and allows the user to
create sessions for interacting with their Cassandra instance.



- generic config
    - contact points
- auth provider
- ExecutionProfile
    - dunno what's necessary to document here
- user types
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

Responsefuture

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

