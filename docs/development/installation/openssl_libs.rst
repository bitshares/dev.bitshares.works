.. role:: strike
    :class: strike
	
.. _openssl-and-lib:

*********************
OpenSSl and another Library
*********************


.. contents:: Table of Contents
   :local:
   
-----------------

Supported OpenSSL Versions
===============================


- OpenSSL: 1.1.0 - **BitShares now supports OpenSSL 1.1.0**
- OpenSSL: 1.0.x series

If your system OpenSSL version is newer than OpenSSL 1.1.0, then you will have to manually provide an older version of OpenSSL and specify it to CMake using ``-DOPENSSL_INCLUDE_DIR``, ``-DOPENSSL_SSL_LIBRARY``, and ``-DOPENSSL_CRYPTO_LIBRARY``. Example::

        cmake -DOPENSSL_INCLUDE_DIR=/usr/include/openssl-1.0 -DOPENSSL_SSL_LIBRARY=/usr/lib/openssl-1.0/libssl.so -DOPENSSL_CRYPTO_LIBRARY=/usr/lib/openssl-1.0/libcrypto.so .

		
|


Build with another Library
===========================

In BitShares-Core, the OpenSSL is linked statically. One of reasons, when we distribute binaries (Windows and also macOS), the libraries need to be statically linked. 


We received a question "Is there any reason for not linking the openssl dynamically? The openssl is linked statically which is causing us troubles when building witness_node for an embedded Linux (RPi)."

* Finding: in order to force dynamic linking on Linux systems which allow both static and dynamic linking, `CMAKE_FIND_LIBRARY_SUFFIXES` must set correct order like this: 
  
   - `SET(CMAKE_FIND_LIBRARY_SUFFIXES .so .a)` - in original CMakeLists.txt it is 
   - `SET(CMAKE_FIND_LIBRARY_SUFFIXES ".a;.so")` that's why the libs are tried to link statically first.

   
|

--------------------


Linking OpenSSL Statically or Dynamically? 
--------------------------------------------

Background Knowledge
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Summary:**

If you use a cUrl library that is **statically** linked with OpenSSL, you will need to statically link to cUrl and the same version of OpenSSL that was used to create the cUrl library.

If you use a cUrl library that is **dynamically** linked with OpenSSL, you will need to dynamically link to cUrl and OpenSSL, and will probably need to use the same version of OpenSSL that was used to build the cUrl library.


**Details:**

Many software projects package up their components into "libraries". These are not usually products by themselves, but pieces of products, or "components". There are many advantages of packaging these things up this way. One of the biggest is being able to use such components in several products.

OpenSSL is a good example of this. They provide some complicated and intricate encryption and security algorithms, package it up, and allow it to be used by thousands of other software projects. cUrl uses it to make SSL connections to remote servers. BitShares uses it for the same thing, as well as in a few other areas.

How you package your component is important. Often, one component is packaged in a variety of different ways to allow others to choose how they want to use it. Such decisions as operating system, hardware, and whether the code should be optimized for speed or being able to trace down problems. Also, the component can be kept separate and "shared" between many applications, or placed inside the application, without the ability to separate one from the other (a.k.a. "static").

cUrl needs the OpenSSL library. BitShares needs OpenSSL and cUrl. When BitShares needs to be compiled into an application, all components must be using the same library (there are exceptions, but those are special cases). So if cUrl was packaged up with (for example) OpenSSL "static", with debug information, and for Linux, BitShares must do the same. Otherwise, the linker (the application that makes other applications) gets confused. "You told me to call a particular OpenSSL function, but I do not know which one you want!"

When Bitshares is compiled, and is using the same OpenSSL components that cUrl did when it was compiled, everyone is happy. There is only 1 version of each OpenSSL function to choose from. The linker is happy, and the application gets built.

.. Note:: 
 - 1: This applies to Windows users as well, although the terminology is a bit different. They call "shared libraries" "DLLs".
 - 2: All of the above should not be taken as rules. There can be some "bending" to make things work, but things will probably be more smooth if you can match all your pieces (versions, products, static/dynamic, etc.).


|

Related Issues & Discussions
------------------------------

- `#1304: dynamically linking openssl <https://github.com/bitshares/bitshares-core/issues/1304>`_ 
- `#1297: make witness_node cli_wallet error <https://github.com/bitshares/bitshares-core/issues/1297>`_ 






|