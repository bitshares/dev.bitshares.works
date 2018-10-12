Note: This is a translation of the document contributed by Abit More. The original can be found here:

https://github.com/abitmore/bts-cn-docs/blob/master/%E4%BD%BF%E7%94%A8VisualStudio2015%E7%BC%96%E8%AF%91BitShares-Core.txt

Compile BitShares-Core with Visual Studio 2015


0. Environmental preparation

* 64-bit Windows systems, such as Windows Server 2012 R2 Standard edition


1. Install Visual Studio 2015 with Update 1

Note:
* There is a problem compiling with VS 2015 Update 3; https://github.com/bitshares/bitshares-core/issues/389
* I have not tried other versions


1.1 Download VS 2015 Update 1

http://download.microsoft.com/download/5/7/A/57A99666-126E-42FA-8E70-862EDBADD215/vs2015.1.com_enu.iso
Visual Studio Community 2015 with Update 1 (x86 and x64) - DVD (English)
SHA1: FB5AE6B57BDC495AFB29646AFCA088756363A263

1.2 Using Virtual Drive Software to Load the above iso Files

If you do not have a virtual drive software, you can use WinCDEmu, download the address http://wincdemu.sysprogs.org/download/

1.3 Installing VS 2015

Select C++ when installing


2. Compile OpenSSL

2.0 References

Http://p-nand-q.com/programming/windows/building_openssl_with_visual_studio_2013.html
Http://developer.covenanteyes.com/building-openssl-for-visual-studio/
Https://www.npcglib.org/~stathis/blog/precompiled-openssl/
Http://blog.csdn.net/fireroll/article/details/51242518

Note: Although some of the above links provide compiled OpenSSL libraries for download, I encountered problems in the process of using them, and finally I solved them from the source code.


2.1 download and install active perl

Https://www.activestate.com/activeperl/downloads


2.2 Download Installation NASM

Http://www.nasm.us/
Http://www.nasm.us/pub/nasm/releasebuilds/2.13.01/win64/nasm-2.13.01-installer-x64.exe

If you do not install NASM, compiling OpenSSL will report errors that ml64 could not find (although there are other ways to resolve it, not described here).


2.3 Downloading OpenSSL Source Code

Note: You can use OpenSSL 1.0.1 or 1.0.2. This article uses 1.0.1u as an example. Cannot use OpenSSL 1.1.0.

Https://www.openssl.org/source/
Https://www.openssl.org/source/openssl-1.0.2l.tar.gz
Https://www.openssl.org/source/old/1.0.1/openssl-1.0.1u.tar.gz

After decompression after downloading, for example, the source code directory is C:\bts\openssl-1.0.1u (do not have multiple nested directories)


2.4 Compiling

Running VS2015 x64 Native Tools Command Prompt
Note: It is actually a shortcut, here:
    C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Visual Studio 2015\Visual Studio Tools\Windows Desktop Command Prompts
    Content is
    %comspec% /k ""C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat"" amd64

Then execute the following command in the open command line interface (Please modify the actual directory according to the actual situation):

Set PATH=C:\Program Files\NASM;%PATH%

c:
Cd C:\bts\openssl-1.0.1u
Perl Configure VC-WIN64A --prefix=C:\bts\openssl-1.0.1u-x64-release-static
Ms\do_win64a
Nmake -f ms\nt.mak
Nmake -f ms\nt.mak install

Problems encountered and solutions:
* If the problem of NASM cannot be found due to PATH, after adding PATH, you need to execute the nmake clean command to clear the temporary file generated before, or delete the source code and unzip it again.
  Then start the compilation step from scratch, otherwise you will encounter the problem that the asm temporary file is empty.
* https://stackoverflow.com/questions/31595869/how-to-resolve-the-module-machine-type-x86-conflicts-with-target-machine-type

After the compilation is completed, a C:\bts\openssl-1.0.1u-x64-release-static directory will be generated, which is a compiled library file.


3. Compile Boost

Http://www.boost.org/
Https://sourceforge.net/projects/boost/files/boost/1.57.0/
Https://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.zip/download

Note:
* Only supports 1.57 - 1.60, 1.57.0 is used here. ( 1.60 encountered a command line parameter parsing problem)
* The compiled library is also available for download on the Internet, but I have encountered problems in downloading and using it. Later, I solved it through my own compilation.

Extract the source code to C:\bts\boost_1_57_0 (do not have multiple nested directories)

Run VS2015 x64 Native Tools Command Prompt and execute it inside:

c:
Cd C:\bts\boost_1_57_0
Bootstrap
B2 architecture=x86 address-model=64 --build-type=complete --toolset=msvc-14.0 --threading=multi --variant=release release stage



4. Download CMake

Https://cmake.org/download/
Https://cmake.org/files/v3.9/cmake-3.9.4-win64-x64.zip

Download the zip file and unzip it to C:\bts\cmake-3.9.4-win64-x64 (don't have multiple nested directories)


5. Download Doxygen (optional)

Doxygen is not required. Its usefulness is to generate documentation and online help. For example, in cli_wallet, you can use the gethelp command to see the parameter description of the command.

Http://www.stack.nl/~dimitri/doxygen/download.html
Http://ftp.stack.nl/pub/users/dimitri/doxygen-1.8.13.windows.x64.bin.zip

After downloading extract to C:\bts\doxygen-1.8.13.windows.x64.bin (do not have multiple nested directories)


6. Download and install git

Https://git-scm.com/download/win


7. Download BitShares-Core source code

Find and run Git Bash from the Start menu. In the open command line interface, execute the following command:

Cd /c/bts
Git clone https://github.com/bitshares/bitshares-core
Cd bitshares-core
Git checkout 2.0.170710
Git submodule update --init --recursive

Note:
* Please modify the specific directory according to the actual situation
* 2.0.170710 is the latest stable version of bitshares-core at the time of this writing. If you need to compile other versions, modify accordingly.


8. Run CMake

8.1 Creating a File C:\bts\setenv_x64.bat

@echo off
Set GRA_ROOT=C:\bts
Set OPENSSL_ROOT=%GRA_ROOT%\openssl-1.0.1u-x64-release-static
Set OPENSSL_ROOT_DIR=%OPENSSL_ROOT%
Set OPENSSL_INCLUDE_DIR=%OPENSSL_ROOT%\include
Set BOOST_ROOT=%GRA_ROOT%\boost_1_57_0
Set CMAKE_ROOT=%GRA_ROOT%\cmake-3.9.4-win64-x64
Set DOXYGEN_ROOT=%GRA_ROOT%\doxygen-1.8.13.windows.x64.bin
Set PATH=%BOOST_ROOT%\lib;%CMAKE_ROOT%\BIN;%DOXYGEN_ROOT%;%PATH%

8.2 Run VS2015 x64 Native Tools Command Prompt and execute it inside:

c:
Cd C:\bts
Setenv_x64.bat
Cmake-gui

The cmake interface will pop up.

* Where is the source code: Enter or select bitshares-core source directory C:/bts/bitshares-core
* Where to build the binaries: Enter or choose to compile the output directory, such as C:/bts/bin

Click the Configure button.

If prompted to compile the output directory does not exist, point Yes Create directory

In the popup box,
The first drop-down box Specify the generator for this project Select Visual Studio 14 2015 Win64
The second input box Optional toolset to use (argument to -T)
The following radio box, select Use default native compilers

Click Finish and wait a moment, the Generate button will light up
Click Generate , and wait a moment, the Open Project button will light up
Click Open Project to open Visual Studio


9. Compiling bitshares-core

Visual Studio,
The default is Debug in the upper toolbar, modified to Release
Another option mode is x64 by default and does not require modification

In the Solution Explorer on the right side of the interface, scroll down to find cli_wallet and witness_node. Right-click and select build.

After the compilation is complete, an executable file is generated:
* C:\bts\bin\programs\witness_node\Release\witness_node.exe
* C:\bts\bin\programs\cli_wallet\Release\cli_wallet.e

10. Others

* The above-mentioned compiled witness_node.exe and cli_wallet.exe can be copied to other computers, but need to use both msvcp140.dll and vcruntime140.dll
   Copy to the same directory, under C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\redist\x64\Microsoft.VC140.CRT\.
   It can also be solved by static linking or by installing redistributable. This article does not elaborate.

* If you use cli_wallet.exe to connect to an API server that uses wss, you need to specify the PEM file that contains the server root certificate.
   Refer to https://github.com/bitshares/bitshares-core/issues/314 ,
   For example, download https://curl.haxx.se/ca/cacert.pem and save it as c:\bts\cacert.pem , then execute:

   Set SSL_CERT_FILE=c:/bts/cacert.pem
   Cli_wallet -s wss://bitshares.openledger.info/ws
