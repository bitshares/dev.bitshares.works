
.. _boost-version-issue:

*********************
Boost Versions 
*********************

Supported Versions
======================

* Boost version: between 1.58 and 1.69

Versions of `Boost <http://www.boost.org/>`_ 1.58 through 1.69 are supported. Newer versions may work, but have not been tested. If your system came pre-installed with a version of Boost that you do not wish to use, you may manually build your preferred version and use it with BitShares by specifying it on the CMake command line. The following is an example::

    cmake -DBOOST_ROOT=/path/to/boost -DCMAKE_BUILD_TYPE=Release .
    make

	
The Boost which ships with Ubuntu 14.04 LTS (64-bit) is too old. You need to download the Boost tarball for Boost 1.57.0 (Note, 1.58.0 requires C++14 and will not build on Ubuntu 14.04 LTS (64-bit); this requirement was an accident, see this mailing list post).

::

	BOOST_ROOT=$HOME/opt/boost_1_57_0
	sudo apt-get update
	sudo apt-get install autotools-dev build-essential libbz2-dev libicu-dev python-dev
	wget -c 'http://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.tar.bz2/download' -O boost_1_57_0.tar.bz2
	[ $( sha256sum boost_1_57_0.tar.bz2 | cut -d ' ' -f 1 ) == "910c8c022a33ccec7f088bd65d4f14b466588dda94ba2124e78b8c57db264967" ] || ( echo 'Corrupt download' ; exit 1 )
	tar xjf boost_1_57_0.tar.bz2
	cd boost_1_57_0/
	./bootstrap.sh "--prefix=$BOOST_ROOT"
	./b2 install

Build with specific Boost version::

	cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Release .
	make
		
		
	
-------------

Known Issues
===================

bitshares-core
-------------------

- If built with **boost 1.60** (and perhaps other versions), some parameter styles in command line is not supported. #881
(e.g.)

::

   - ``./cli_wallet -s ws://1.2.3.4:5678/`` works with boost 1.57 and 1.58, but doesn't work with 1.60
   - ``./cli_wallet -sws://1.2.3.4:5678/`` works with boost 1.57, 1.58 and 1.60
   
- ``time_point_sec`` jsonifies to different result in boost 1.64, as mentioned in `#597 <https://github.com/bitshares/bitshares-core/issues/597>`_ 

.. note:: This document is for users of witness_node and cli_wallet, not for the core developers.






|