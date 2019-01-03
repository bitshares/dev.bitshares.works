
***********************
Getting Started 
***********************

.. _installation-guide:

.. contents:: Table of Contents
   :local:
   
-----------------
   
Installation
========================

.. Important::  
   - BitShares requires a 64-bit operating system to build
   - See BitShares :ref:`system requirements <system-requirements-node>`
   

Download and Build
-----------------------

Select an operation system and install and build.

.. toctree:: 
    :maxdepth: 1

    installation/build_ubuntu
    installation/build_osx
    installation/build_windows
    installation/build_windows_devenv
    installation/windows_cli_tool
	

.. toctree:: 
    :maxdepth: 1

    installation/wsl


.. note::
		- BitShares requires a 64-bit operating system to build, and will not build on a 32-bit OS.
		- BitShares now supports Ubuntu 18.04 LTS
		- BitShares now supports OpenSSL 1.1.0

		
Build Script::

	git clone https://github.com/bitshares/bitshares-core.git
	cd bitshares-core
	git checkout master # may substitute "master" with current release tag
	git submodule update --init --recursive
	cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo .
	make

	
Upgrade Script (prepend to the Build Script above if you built a prior release)::

	git remote set-url origin https://github.com/bitshares/bitshares-core.git
	git checkout master
	git remote set-head origin --auto
	git pull
	git submodule update --init --recursive # this command may fail
	git submodule sync --recursive
	git submodule update --init --recursive

-----------

	
Known issues
-------------

.. toctree:: 
    :maxdepth: 1

    installation/boost_versions


------------------------

Test Local Network and Wallet
========================================

.. toctree:: 
    :maxdepth: 2

    apps/after_install
    apps/create_test_local_wallet


------------------------

Next Interests
=====================

.. toctree:: 
    :maxdepth: 2

    next-step
	 
	
|

