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

Some |Cluster| attributes are set by the user and don't change, or only change
via action by the application.
These options (e.g. anything managed by |ExecutionProfiles| are out of scope for this document.
Instead, we'll focus on how |Sessions| and |Clusters| persist necessary mutable
data between |Session| instances.
(Note that, typically, users will create a single |Session| at a time for a
given |Cluster|, only creating more if the first fails.
In fact, in our next major release, the |Cluster| abstraction will no longer be
part of the public API and users will create |Sessions| directly.)

The particular resources that |Clusters| persist for their |Sessions| are:

- registries containing user-defined types and prepared statements,
- a :class:`.Metadata` object that maintains a view of the server's schema
  and associated useful information (e.g., a :class:`map that describes the
  token ring <.TokenMap>`),
- a :class:`.ControlConnection` that the |Cluster| uses to make schema queries
  to maintain its view of the server's metadata,
- a timestamp generator callable that is shared across all |Sessions|,
- a registry of :class:`.HostStateListener`\ s with callbacks that are called
  when a host is added or removed or goes up or down.
- a metrics collector (if the driver is configured to use one), and
- a ``ThreadPoolExecutor`` that manages threads for handling asynchronous
  management tasks.

|Sessions| created by a |Cluster| update the registries of user-defined types
and prepared statements when they recieve responses from the server indicating
that they've been created.
The same is true for updating the :class:`.Cluster`\ 's metadata --
the cluster updates its view of server metadata when it recieves results from
the server indicating that something has changed.
These are particular instances of response processing; see the
:ref:`response-processing` section for more details on response processing in
general.


.. |Session| replace:: :class:`.Session`
.. |Sessions| replace:: :class:`.Session`\ s
.. |Cluster| replace:: :class:`.Cluster`
.. |Clusters| replace:: :class:`.Cluster`\ s
.. |ExecutionProfiles| replace:: :class:`.ExecutionProfile`\ s
