I/O Reactors
============

The driver handles I/O by deferring to asynchronous execution engines called
*reactors*. Because different applications and libraries in the Python
ecosystem use many different reactors, we support :doc:`several different reactors <io>`.
