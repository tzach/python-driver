Clusters and Sessions
=====================

The :class:`.Cluster` object is typically the main object an application
interacts with when using the driver.
It provides methods for configuring the connection to the Cassandra cluster and
holds the objects that maintain those connections.

- generic config
    - contact points
- auth provider
- ExecutionProfile
    - ...
- user types
- address translator
- metrics collector
- timestamp generator
- metadata
- connections
    - control connection
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

responsefuture
- callbacks
resultset
- paging
