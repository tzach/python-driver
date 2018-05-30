System Architecture
===================

This documentation shows the structure of the driver. Its goal is to help new
driver developers understand how to change behavior and to help advanced users
extending the driver understand how the driver uses their extensions.

:doc:`cluster`
    The central objects in typical driver use.

:doc:`reactors`
    The underlying asynchronous event engines that the driver uses for I/O.


.. toctree::
    :maxdepth: 3

    dev_guide
    dev_guide/reactors
    dev_guide/cluster

..
   Java TOC
   - native protocol layer (codecs)
   - Netty pipeline
     - SslHandler
     - FrameEncoder and FrameDecoder
     - HeartbeatHandler
     - InflightHandler
     - ProtocolInitHandler
   - Request execution
     - Connection pooling
     - Request processors
     - Request lifecycle
   - Administrative tasks
     - Control connection
     - Node state manager
       - Topology events vs Node state events
     - Metadata manager
     - Topology monitor
     - How admin components interact
       - A new node gets added
       - A new table gets created
       - The last connection to an active node drops
   - Common infrastructure and concepts
     - Context
     - Event bus
     - Concurrency
       - Hot path
       - Cold path


