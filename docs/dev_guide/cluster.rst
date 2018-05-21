Cluster
=======

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
- address translator
- timestamp generator
