
.. _build-osx:

**********************
Building on OS X
**********************


.. contents:: Table of Contents
   :local: 
   
----------------


BitShares OS X Build Instructions
==================================

1. Install XCode and its command line tools

**Instructions** here: https://guide.macports.org/#installing.xcode.

In OS X 10.11 (El Capitan) and newer, you will be prompted to install developer tools when running a devloper command in the terminal. This step may not be needed.

2. Install Homebrew

**Instructions** here: http://brew.sh/

3. Initialize Homebrew::

    brew doctor
    brew update

4. Install dependencies::

    brew install cmake git openssl autoconf automake 
    brew link --force openssl 
    brew install bitshares/boost/boost@1.69
    brew link --force --overwrite boost@1.69

5. (Optional) To support importing Bitcoin wallet files:

    brew install berkeley-db

6. (Optional) To use TCMalloc in LevelDB::

    brew install google-perftools

7. Clone the Bitshares repository::

    git clone https://github.com/bitshares/bitshares-core.git
    cd bitshares-core

8. Build BitShares::

    git submodule update --init --recursive
    cmake .
    make

	
.. Notes:: As mentioned elsewhere, Bitshares depends on the third-party libraries "Boost" and "OpenSSL". These libraries need to be in certain version ranges. At the moment, Boost needs to be **between 1.58 and 1.74**. OpenSSL needs to be in the **1.0.x - 1.1.x range**.


------------

Available Versions
====================

Boost
-----------

- Boost: between 1.58 and 1.74

To install the default version of boost::

    brew install boost

To install an older version of boost from our own HomeBrew Tap::

    brew install bitshares/boost/boost@1.69

OpenSSL
----------

- OpenSSL: 1.0.x - 1.1.x series

You may have an older version of OpenSSL than is required. If so, have brew get the latest::

    brew upgrade openssl

Compiling with these new versions: We must now tell cmake where these libraries are. Instead of the "cmake ." mentioned above, we use::

    cmake -DBOOST_ROOT=/usr/local/opt/boost@1.69 -DOPENSSL_ROOT_DIR=/usr/local/opt/openssl .

and then proceed with the normal::

    make

|
