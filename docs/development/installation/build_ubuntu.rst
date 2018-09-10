
.. _build-ubuntu:


********************
Building on Ubuntu
******************** 
 
.. contents:: Table of Contents
   :local: 

--------

Ubuntu 16.04 LTS (64-bit)
=============================

1. Install Dependencies
  We recommend building on Ubuntu 16.04 LTS (64-bit), and the build dependencies may be installed with::

    sudo apt-get update
    sudo apt-get install autoconf cmake make automake libtool git libboost-all-dev libssl-dev g++ libcurl4-openssl-dev

2. Build BitShares Core::

    git clone https://github.com/bitshares/bitshares-core.git
    cd bitshares-core
    git checkout <LATEST_RELEASE_TAG>
    git submodule update --init --recursive
    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo .
    make

---------------------

Ubuntu 14.04 LTS (64-bit)
=============================

(*See the instruction to build Newer version of Boost separately*)

1. Install Dependencies

This is necessary for clean installation.::

    sudo apt-get update
    sudo apt-get install cmake make libbz2-dev libdb++-dev libdb-dev libssl-dev openssl libreadline-dev autoconf libtool git ntp libcurl4-openssl-dev g++ libcurl4-openssl-dev

2. Build Boost 1.57.0

Download the Boost tarball for Boost 1.57.0. (*Ubuntu 14.04 ships old version of Boost.) 

.. Note:: 1.58.0 requires C++14 and will not build on Ubuntu 14.04 LTS; this requirement was an accident, see this mailing list post.

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

3. Build BitShares Core

::

    cd ..
    git clone https://github.com/bitshares/bitshares-core.git
    cd bitshares-core
    git submodule update --init --recursive
    cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Release .
    make 


---------------

Available Software Versions
===============================

Ubuntu
----------

We recommend building on Ubuntu 16.04 LTS (64-bit), and the build dependencies may be installed with:

- Ubuntu 16.04 LTS (**64-bit**)
 
 
.. Note:: BitShares requires a 64-bit operating system to build, and will not build on a 32-bit OS.

Boost
---------

- Boost: between 1.57 and 1.65

Versions earlier than 1.57 or newer than 1.65 are NOT supported. If your system Boost version is newer, then you will need to manually build an older version of Boost and specify it to CMake using `-DBOOST_ROOT`. Example::

        cmake -DBOOST_ROOT=~/boost160 .

OpenSSL
---------

- OpenSSL: 1.0.x series

OpenSSL 1.1.0 and newer are NOT supported. If your system OpenSSL version is newer, then you will need to manually provide an older version of OpenSSL and specify it to CMake using ``-DOPENSSL_INCLUDE_DIR``, ``-DOPENSSL_SSL_LIBRARY``, and ``-DOPENSSL_CRYPTO_LIBRARY``. Example::

        cmake -DOPENSSL_INCLUDE_DIR=/usr/include/openssl-1.0 -DOPENSSL_SSL_LIBRARY=/usr/lib/openssl-1.0/libssl.so -DOPENSSL_CRYPTO_LIBRARY=/usr/lib/openssl-1.0/libcrypto.so .

---------------

Known issue: 
------------

Error ``{"message":"Timer Expired"}`` in Ubuntu 16.04 LTS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If error ``{"message":"Timer Expired"}`` dropped then it could be issue with websocketpp in linux kernel > 4.4.

Details `here <https://github.com/DECENTfoundation/DECENT-Network/issues/194>`_ 

Steps to fix::

    cd ~/bitshares-core/libraries/fc/vendor/websocketpp
    git remote set-url origin https://github.com/DECENTfoundation/websocketpp.git
    git fetch
    git checkout 

And then build BitShares Core.


|

|



