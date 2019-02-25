.. _build-windows-devenv:

************************************************
Windows Development Environment setup and build instruction
************************************************

.. contents:: Table of Contents
   :local: 

----

Prerequisites
===============================
This is an informal build environment setup instruction, it is not recommended to apply in production.

* 64 bit windows operating system
* Microsoft Visual C++ 2015 Update 1 - Download address can be found at https://gist.github.com/CHEF-KOCH/ebd07873bb4b343613db (the community
  edition will work, any newer version of visual studio will not work)
  
  
* git for windows (Git-2.18.0-64-bit)
* cmake (cmake-3.12.1-win64-x64)

Any other version of the software listed above is not used nor tested, but can still be used on your own risk. To make things easier, we suggest you keep all the default settings in the install process and add both git and cmake into your PATH

Download and install libraries
=======================================

* boost 1.65.1 https://sourceforge.net/projects/boost/files/boost-binaries/1.65.1/boost_1_65_1-msvc-14.0-64.exe/download
* Openssl 1.0.2p https://slproweb.com/download/Win64OpenSSL-1_0_2p.exe

You do can download the source and compile the two libraries above, it will take more time and return more safety/security. Again, we still suggest you to keep all the default settings in the install process. Any changes you made here, you have to make the corresponding changes to cmake command we use later.

Download and Compile
==============================

1. cURL, we do not find any usable prebuilt cURL, so we have to compile it from source. Open a command window with administrator right, and run

::

	git clone https://github.com/curl/curl.git
	cd curl
	buildconf.bat
	cmake -G "Visual Studio 14 2015 Win64" -DCMAKE_USE_OPENSSL=ON -DCURL_DISABLE_FTP=ON -DCURL_DISABLE_LDAP=ON -DCURL_DISABLE_TELNET=ON -DCURL_DISABLE_DICT=ON -DCURL_DISABLE_FILE=ON -DCURL_DISABLE_TFTP=ON -DCURL_DISABLE_LDAPS=ON -DCURL_DISABLE_RTSP=ON -DCURL_DISABLE_POP3=ON -DCURL_DISABLE_IMAP=ON -DCURL_DISABLE_SMTP=ON -DCURL_DISABLE_GOPHER=ON -DCURL_STATICLIB=ON -DOPENSSL_CONF_SOURCE="C:\OpenSSL-Win64\bin\openssl.cfg"
	cmake --build . --target install --config Release -j 3

The command above will compile the libcURL and install it to the C:\Program Files\CURL directory.

2. compile the bitsares-core. In the command windows we opened before, run

::

	git clone https://github.com/bitshares/bitshares-core.git
	git checkout develop
	git submodule update --init --recursive
	cmake  -G "Visual Studio 14 2015 Win64" -DBoost_USE_STATIC_LIBS=ON -DOPENSSL_CONF_SOURCE="C:\OpenSSL-Win64\bin\openssl.cfg" -DCURL_INCLUDE_DIR="C:\Program Files\CURL\include" -DCURL_LIBRARY="C:\Program Files\CURL\lib\libcurl_imp.lib" -DCURL_STATICLIB=ON
	cmake --build . --target install --config Release -j 3

If you want to dynamically link to cURL, you need to drop the -DCURL_STATICLIB=ON argument. The number after -j in the last command is recommended set to N-1, where N is your computer's core number.


|



