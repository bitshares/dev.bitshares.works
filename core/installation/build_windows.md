## Build BitShares-Core with Visual Studio 2015

#### Table of Contents:
- [Environmental preparation](#environmental-preparation)
- [Setup Visual Studio 2015 with Update 1](#setup-visual-studio-2015-with-update-1)
- [Download Software and Tools](#download-software-and-tools)
- [Build the Library Dependencies](#build-the-library-dependencies)
- [BitShares-Core](#bitshares-core)
- [Set up the Environment for Build](#set-up-the-environment-for-build)
- [Visual Studio](#visual-studio)

***


### Environmental preparation

* 64-bit Windows systems, such as Windows Server 2012 R2 Standard edition
* Visual Studio 2015 with Update 1 (*our test Completed successfully*)

## Setup Visual Studio 2015 with Update 1

*Known Issue: There is a problem compiling with VS 2015 Update 3; https://github.com/bitshares/bitshares-core/issues/389*

### 1.Download Visual Studio 2015 Update 1

        http://download.microsoft.com/download/5/7/A/57A99666-126E-42FA-8E70-862EDBADD215/vs2015.1.com_enu.iso
        Visual Studio Community 2015 with Update 1 (x86 and x64) - DVD (English)
        SHA1: FB5AE6B57BDC495AFB29646AFCA088756363A263

### 2.Virtual Drive Software to Load the above iso Files 

If you do not have a virtual drive software, you can use WinCDEmu;
- Download from here: http://wincdemu.sysprogs.org/download/

### 3.Installing Visual Studio 2015 Update 1

- Select C++ when installing

## Download Software and Tools

### 4.Active perl 
- Download and install 

If you do not have `perl` software in your machine, install it. You'll need it later.

        https://www.activestate.com/activeperl/downloads

### 5.NASM 
- Download and install NASM

If you do not install NASM, compiling OpenSSL will report errors that ml64 could not find (although there are other ways to resolve it, not described here).

        http://www.nasm.us/
        http://www.nasm.us/pub/nasm/releasebuilds/2.13.01/win64/nasm-2.13.01-installer-x64.exe


### 6.OpenSSL
BitShares Core depends on OpenSSL version 1.0.1 or 1.0.2, and you must build this from source. This article uses 1.0.1u as an example. Cannot use OpenSSL 1.1.0.

*References*

        http://p-nand-q.com/programming/windows/building_openssl_with_visual_studio_2013.html
        http://developer.covenanteyes.com/building-openssl-for-visual-studio/
        https://www.npcglib.org/~stathis/blog/precompiled-openssl/
        http://blog.csdn.net/fireroll/article/details/51242518

Note: Although some of the above links provide compiled OpenSSL libraries for download, I encountered problems in the process of using them, and finally I solved them from the source code.

- Download OpenSSL Source Code from;

            https://www.openssl.org/source/
            https://www.openssl.org/source/openssl-1.0.2l.tar.gz
              or
            https://www.openssl.org/source/old/1.0.1/openssl-1.0.1u.tar.gz

- Decompression to the base directory `C:\bts\`.
- the source code directory is `C:\bts\openssl-1.0.1u` (do not have multiple nested directories)

### 7.Boost
BitShares Core depends on the Boost libraries, only version 1.57 ~ 1.60. 1.57.0 is used here. ( 1.60 encountered a command line parameter parsing problem)


- Download OpenSSL Source Code from;

        http://www.boost.org/
        https://sourceforge.net/projects/boost/files/boost/1.57.0/
        https://sourceforge.net/projects/boost/files/boost/1.57.0/boost_1_57_0.zip/download

Note: The compiled library is also available for download on the Internet, but I have encountered problems in downloading and using it. Later, I solved it through my own compilation.

- Extract (unzip) the source code to the base directory `C:\bts\`.
- the source code directory is `C:\bts\boost_1_57_0` (do not have multiple nested directories)

### 8.CMake
- Download the CMake from;

        https://cmake.org/download/
        https://cmake.org/files/v3.9/cmake-3.9.4-win64-x64.zip

- Extract (unzip) the source code to the base directory `C:\bts\`.
- the source code directory is `C:\bts\cmake-3.9.4-win64-x64` (do not have multiple nested directories)


## Build the Library Dependencies

### 9.Build OpenSSL DLLs
- Run **VS2015 x64 Native Tools Command Prompt**

Note: It is actually a shortcut, here: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Visual Studio 2015\Visual Studio Tools\Windows Desktop Command Prompts
    Content is
    
    %comspec% /k ""C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat"" amd64

- Execute the following command in the open command line interface 

> Please modify the actual directory according to the actual situation.

    set PATH=C:\Program Files\NASM;%PATH%

    c:
    cd C:\bts\openssl-1.0.1u
    perl Configure VC-WIN64A --prefix=C:\bts\openssl-1.0.1u-x64-release-static
    ms\do_win64a
    nmake -f ms\nt.mak
    nmake -f ms\nt.mak install

- After the compilation is completed, a `C:\bts\openssl-1.0.1u-x64-release-static` directory will be generated, which is a compiled library file.

**Problems encountered and solutions:**

* If the problem of NASM cannot be found due to PATH, after adding PATH, you need to execute the nmake clean command to clear the temporary file generated before, or delete the source code and unzip it again.
  Then start the compilation step from scratch, otherwise you will encounter the problem that the asm temporary file is empty.
* https://stackoverflow.com/questions/31595869/how-to-resolve-the-module-machine-type-x86-conflicts-with-target-machine-type



### 10.Build Boost
- Run **VS2015 x64 Native Tools Command Prompt** and execute the below commands:

        c:
        cd C:\bts\boost_1_57_0
        bootstrap
        b2 architecture=x86 address-model=64 --build-type=complete --toolset=msvc-14.0 --threading=multi --variant=release release stage


### 11.Doxygen (optional)

Doxygen is not required. Its usefulness is to generate documentation and online help. For example, in cli_wallet, you can use the gethelp command to see the parameter description of the command.

- Download Doxygen

        http://www.stack.nl/~dimitri/doxygen/download.html
        http://ftp.stack.nl/pub/users/dimitri/doxygen-1.8.13.windows.x64.bin.zip

- Extract it to `C:\bts\doxygen-1.8.13.windows.x64.bin` (do not have multiple nested directories)


### 12.git
- Download and install git

       Https://git-scm.com/download/win


## BitShares-Core

### 13.Download and Install BitShares-Core Source code

Find and run `Git Bash` from the Start menu. In the open command line interface, execute the following command:

    cd /c/bts
    git clone https://github.com/bitshares/bitshares-core
    cd bitshares-core
    git checkout <LATEST_RELEASE_TAG>
    git submodule update --init --recursive

Note:
* Please modify the specific directory according to the actual situation
* Please replace `<LATEST_RELEASE_TAG>` by the latest release version of bitshares-core. If you need to compile other versions, modify accordingly. Find [BitShares Core latest release](https://github.com/bitshares/bitshares-core/releases).

**At the end of this, your base directory should look like this**

                c:\bts
                +- bitshares-core
                +- boost_1_57_0
                +- cmake-3.9.4-win64-x64
                +- openssl-1.0.1u
                +- openssl-1.0.1u-x64-release-static
                +- doxygen-1.8.13.windows.x64.bin (if you downloaded)


## Set up the Environment for Build


### 14.Create a File C:\bts\setenv_x64.bat

- Add the below lines and save it.

        @echo off
        Set GRA_ROOT=C:\bts
        Set OPENSSL_ROOT=%GRA_ROOT%\openssl-1.0.1u-x64-release-static
        Set OPENSSL_ROOT_DIR=%OPENSSL_ROOT%
        Set OPENSSL_INCLUDE_DIR=%OPENSSL_ROOT%\include
        Set BOOST_ROOT=%GRA_ROOT%\boost_1_57_0
        Set CMAKE_ROOT=%GRA_ROOT%\cmake-3.9.4-win64-x64
        
        Set DOXYGEN_ROOT=%GRA_ROOT%\doxygen-1.8.13.windows.x64.bin
        
        Set PATH=%BOOST_ROOT%\lib;%CMAKE_ROOT%\BIN;%DOXYGEN_ROOT%;%PATH%


- Run **VS2015 x64 Native Tools Command Prompt** 

- Execute the below commands:

        c:
        cd C:\bts
        setenv_x64.bat
        cmake-gui

> The cmake interface will pop up.


###  15.CMake GUI

- Set the values in the CMake GUI
  - Where is the source code: Enter or select bitshares-core source directory `C:/bts/bitshares-core`
  - Where to build the binaries: Enter or choose to compile the output directory, such as `C:/bts/bin`

- Click the [Configure] button.
  - If prompted to compile the output directory does not exist, point [Yes] Create directory
- In the popup box,
  - The first drop-down box Specify the generator for this project Select **Visual Studio 14 2015 Win64**
  - The second input box Optional toolset to use (argument to -T)
  - The following radio box, select Use **default native compiler**
  - Click [Finish] and wait a moment, the Generate button will light up
- Click [Generate] , and wait a moment, the Open Project button will light up
- Click [Open Project] to open Visual Studio


## Visual Studio

### 16.Build 

- Compile Bitshares-core
  - The default is `Debug` in the upper toolbar, modified to `Release`
  - another option mode is `x64` by default and does not require modification
- Build two executable 
  - In the Solution Explorer on the right side of the interface, scroll down to find **cli_wallet** and **witness_node**. Right-click and select build.

After the compilation is complete, an executable file is generated:

        * C:\bts\bin\programs\witness_node\Release\witness_node.exe
        * C:\bts\bin\programs\cli_wallet\Release\cli_wallet.exe

## Others

#### Your next step...

* The above-mentioned compiled `witness_node.exe` and `cli_wallet.exe` can be copied to other computers, but need to use both `msvcp140.dll` and `vcruntime140.dll`

   Copy to the same directory, under `C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\redist\x64\Microsoft.VC140.CRT\`.
   It can also be solved by static linking or by installing redistributable. This article does not elaborate.

* If you use `cli_wallet.exe` to connect to an API server that uses wss, you need to specify the PEM file that contains the server root certificate.
  - Refer this page - [CLI-Wallet on Windows (x64)](../installation/windows_cli_tool.md#cli-wallet-on-windows-x64)
       
***

#### Contributor:
- @abit

Note: This is a translation of the document contributed by Abit More. The original can be found here:

-https://github.com/abitmore/bts-cn-docs/blob/master/%E4%BD%BF%E7%94%A8VisualStudio2015%E7%BC%96%E8%AF%91BitShares-Core.txt

Also, referenced the below

- https://github.com/bitshares/bitshares-core/wiki/BUILD_WIN32



