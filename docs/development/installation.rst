
***********************
Getting Started 
***********************

.. _installation-guide:

Installation
========================

BitShares Core is the BitShares blockchain implementation and command-line interface. The web wallet is `BitShares UI <https://github.com/bitshares/bitshares-ui>`_.

Visit `BitShares.org <https://bitshares.org/>`_ to learn about BitShares and join the community at `BitSharesTalk.org <https://bitsharestalk.org/>`_.

**NOTE:** The official BitShares git repository location, default branch, and submodule remotes were recently changed. Existing repositories can be updated with the following steps::

	git remote set-url origin https://github.com/bitshares/bitshares-core.git
	git checkout master
	git remote set-head origin --auto
	git pull
	git submodule sync --recursive
	git submodule update --init --recursive
	
	
.. Attention:: See BitShares :ref:`system requirements <system-requirements-node>`  for a node (updated: 2018-07-02).


Download and Build
-----------------------

Select an operation system and install and build.

.. toctree:: 
    :maxdepth: 1

    installation/build_ubuntu
    installation/build_osx
    installation/build_windows
    installation/windows_cli_tool
	

Build and Run BitShares-Core in WSL (Installation Option)
---------------------------------------------------------

.. toctree:: 
    :maxdepth: 1

    installation/wsl
	
Known issues
-------------

.. toctree:: 
    :maxdepth: 1

    installation/boost_versions


------------------------

	
|

