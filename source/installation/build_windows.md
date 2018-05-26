# Building on Windows

## Windows - Visual Studio 2013 

### Prerequisites:

- Microsoft Visual C++ 2013 Update 1 (the free Express edition will work)
- If you have multiple MSVS installation use MSVS Developer console from target version.
- This build is for 64bit binaries.

***

## Setup the Directory Structure

### 1.Create a Base Directory for All Project.

In this example, we createthe project folder `D:\bitshares`.  This directory will be referred we `GRA_ROOT` in several of the batch files and make files.

    mkdir D:\bitshares

### 2.Clone the BitShares Core repository

    D:
    cd D:\bitshares
    git clone https://github.com/bitshares/bitshares-core.git
    cd bitshares-core
    git submodule update --init --recursive

### 3.Download CMake

- Download the latest *Win32 Zip* build CMake from http://cmake.org/cmake/resources/software.html (version 2.8.12.2 as of this writing). 
- Unzip it to your base directory'
- Rename the folder name (if it's different) to `D:\bitshares\**CMake**`.
 
(e.g.) Rename from `D:\bitshares\cmake-2.8.12.2-win32-x86` to `D:\bitshares\CMake`.

> Note: If you already have CMake installed elsewhere on your system, you can use it. However, BitShares Core has a few batch files that expect it to be in the base directory's `CMake` subdirectory, so those scripts would need tweaking.

### 3.Boost

BitShares Core depends on the Boost libraries version 1.57 ~ 1.65. You can build them from source.

- download boost source from http://www.boost.org/users/download/
- unzip it to the base directory `D:\bitshares`.
- This will create a directory like `D:\bitshares\boost_1_57_0`.


### 4.OpenSSL

BitShares Core depends on OpenSSL version 1.0.1 or 1.0.2, and you must build this from source.
   
- download OpenSSL source from http://www.openssl.org/source/
- Untar it to the base directory `D:\bitshares`.
- this will create a directory like `D:\bitshares\openssl-1.0.1g`.

#### At the end of this, your *base directory* should look like this (directory names will be slightly different for the 64bit versions):

    D:\bitshares
    +- bitshares-core
    +- boost_1_57_0
    +- CMake
    +- openssl-1.0.1g


## Build the Library Dependencies

### 5.Set up Environment for Building:
#### 5.1.Create a file `setenv_x64.bat`

    D:
    cd D:\bitshares
    notepad setenv_x64.bat

#### 5.2.Put the below lines into the notepad window (setenv_x64.bat file), then save and quit.

    @echo off
    set GRA_ROOT=d:\bitshares
    set OPENSSL_ROOT=%GRA_ROOT%\openssl-1.0.1g
    set OPENSSL_ROOT_DIR=%OPENSSL_ROOT%
    set OPENSSL_INCLUDE_DIR=%OPENSSL_ROOT%\include
    set BOOST_ROOT=%GRA_ROOT%\boost_1_57_0

    set PATH=%GRA_ROOT%\CMake\bin;%BOOST_ROOT%\lib;%PATH%

    echo Setting up VS2013 environment...
    call "%VS120COMNTOOLS%\..\..\VC\vcvarsall.bat" x86_amd64

#### 5.3.Run the file:

    setenv_x64.bat

### 6.Build OpenSSL DLLs

    D:
    cd D:\bitshares\openssl-1.0.1g
    perl Configure VC-WIN64A --prefix=D:\bitshares\OpenSSL
    ms\do_win64a
    nmake -f ms\ntdll.mak
    nmake -f ms\ntdll.mak install

This will create the directory `D:\bitshares\OpenSSL` with the libraries, DLLs, and header files.

### 7.Build Boost

    D:
    cd D:\bitshares\boost_1_57_0
    bootstrap
    .\b2.exe address-model=64

## Build Project Files for BitShares Core

### 8.CMake

#### 8.1.Create a file `run_cmake_x64.bat`

    D:
    cd D:\bitshares\bitshares-core
    notepad run_cmake_x64.bat

#### 8.2.Put the below lines into the notepad window (run_cmake_x64.bat file), then save and quit.

    setlocal
    call "d:\bitshares\setenv_x64.bat"
    cd %GRA_ROOT%
    cmake-gui -G "Visual Studio 12"

#### 8.3.Run the file

    run_cmake_x64.bat

This pops up the cmake *GUI*. 

#### However, if you've used CMake before it will probably be showing the wrong data. To fix the issue, check 
- Where is the source code: `D:\bitshares\bitshares-core`
- Where to build the binaries: `D:\bitshares\x64`

### 9.CMake GUI

#### 9.1.In the cmake GUI, click `Configure`

It may ask you to specify a generator for this project; 
if it does, choose **Visual Studio 12 2013 Win64** for 64 bit builds and select **Use default native compilers**. 

**Look through the output and fix any errors**

#### 9.2.Click `Generate`

### 10.Visual Studio

#### 10.1.Launch Visual Studio and load `D:\bitshares\x64\BitShares.sln`

#### 10.2.Set Active Configuration to `RelWithDebInfo`, ensure Active Solution platform is `x64 `for 64 bit builds

#### 10.3.`Build` Solution 

Or you can build the `INSTALL` target in Visual Studio which will copy all of the necessary files into your `D:\bitshares\install` directory, then copy all of those files to the bin directory.


***
