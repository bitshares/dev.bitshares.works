.. role:: strike
    :class: strike
	
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
    git submodule update --init --recursive
    cmake -DCMAKE_BUILD_TYPE=Release  .
    make

---------------------

**Build Script**

::

	git clone https://github.com/bitshares/bitshares-core.git
	cd bitshares-core
	git checkout master # may substitute "master" with current release tag
	git submodule update --init --recursive
	cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo .
	make

	
**Upgrade Script (prepend to the Build Script above if you built a prior release)**

::

	git remote set-url origin https://github.com/bitshares/bitshares-core.git
	git checkout master
	git remote set-head origin --auto
	git pull
	git submodule update --init --recursive # this command may fail
	git submodule sync --recursive
	git submodule update --init --recursive


|


---------------------

Supported Ubuntu Versions
--------------------------

We recommend building on Ubuntu 16.04 LTS (64-bit), and the build dependencies may be installed with:

- Ubuntu 18.04 LTS (**64-bit**)
- Ubuntu 16.04 LTS (**64-bit**)
 
.. Note:: BitShares requires a 64-bit operating system to build, and will not build on a 32-bit OS.


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



