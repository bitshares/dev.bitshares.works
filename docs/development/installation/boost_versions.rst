
.. _boost-version-issue:

*********************
Boost Versions 
*********************

Supported Versions
======================

* Boost version: between 1.58 and 1.74

Versions of `Boost <http://www.boost.org/>`_ 1.58 through 1.74 are supported. Newer versions may work, but have not been tested. If your system came pre-installed with a version of Boost that you do not wish to use, you may manually build your preferred version and use it with BitShares by specifying it on the CMake command line. The following is an example::

    cmake -DBOOST_ROOT=/path/to/boost -DCMAKE_BUILD_TYPE=Release .
    make
	
-------------

Known Issues
===================

bitshares-core
-------------------

- If built with **boost 1.60** (and perhaps other versions), some parameter styles in command line is not supported. https://github.com/bitshares/bitshares-core/issues/881
(e.g.)

::

   - ``./cli_wallet -s ws://1.2.3.4:5678/`` works with boost 1.58, but doesn't work with 1.60
   - ``./cli_wallet -sws://1.2.3.4:5678/`` works with boost 1.58 and 1.60
   
- ``time_point_sec`` jsonifies to different result in boost 1.64, as mentioned in `#597 <https://github.com/bitshares/bitshares-core/issues/597>`_ 

.. note:: This document is for users of witness_node and cli_wallet, not for the core developers.






|
