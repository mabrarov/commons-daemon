# Commons Daemon Native CMake project

[![AppVeyor CI Build Status](https://ci.appveyor.com/api/projects/status/sb54phb5i0r14i66?svg=true)](https://ci.appveyor.com/project/mabrarov/commons-daemon)

## Prerequisites

1. [CMake](https://cmake.org/)

    This CMake project was tested with [CMake 3.11.1](https://cmake.org/files/v3.11/cmake-3.11.1-win64-x64.zip).
    
1. [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

    This CMake project was tested with JDK 1.8 update 162.

## Building

It's assumed that `CMDM_ROOT` is the directory where this git repository is cloned into.

1. `CMAKE_USER_MAKE_RULES_OVERRIDE` option should point to `CMDM_ROOT/src/native/windows/apps/cmake/static_c_runtime_overrides.cmake` if building with static C/C++ runtime.
1. `CMAKE_USER_MAKE_RULES_OVERRIDE_CXX` option should point to `CMDM_ROOT/src/native/windows/apps/cmake//static_cxx_runtime_overrides.cmake` if building with static C/C++ runtime.
1. `JAVA_HOME` can be used as a hint for searching for JDK. It should point to the directory where JDK is installed. Usage of forward slash (`/`) as path delimiter is recommended over usage of backward slash (`\`) due to FindJNI CMake module.  
1. `WINVER` option can be used to specify target Windows version. Allowed values (one of) are:
    * `WINNT` (Windows 2000 and up) 
    * `WINXP` (Windows XP and up, default value)
    * `WIN2003` (Windows 2003 and up)
    * `VISTA` (Windows Vista and up)
    * `WIN7` (Windows 7 and up)

## Example for generation of project

Example of command line for generation of Visual Studio 2015 project with below parameters:

1. `C:\Users\Marat\Documents\work\cpp\commons-daemon\src\native\windows\apps` is the same directory where this `README.md` is located, i.e. this git repository is cloned into `C:\Users\Marat\Documents\work\cpp\commons-daemon` directory. 
1. `CMAKE_USER_MAKE_RULES_OVERRIDE` and `CMAKE_USER_MAKE_RULES_OVERRIDE_CXX` are required to use static C/C++ runtime, i.e `prunsrv.exe` will be built with static C/C++ runtime.
1. `C:/Program Files/Java/jdk1.8.0_162` is the directory where JDK is installed. 
1. `Visual Studio 14 2015 Win64` is [CMake generator for Visual Studio 2015 project](https://cmake.org/cmake/help/v3.1/generator/Visual%20Studio%2014%202015.html) building 64-bit binaries.

Current directory is the directory where generated Visual Studio 2015 project will be placed. 

```cmd
cmake -D CMAKE_USER_MAKE_RULES_OVERRIDE=C:\Users\Marat\Documents\work\cpp\commons-daemon\src\native\windows\apps\cmake\static_c_runtime_overrides.cmake -D CMAKE_USER_MAKE_RULES_OVERRIDE_CXX=C:\Users\Marat\Documents\work\cpp\commons-daemon\src\native\windows\apps\cmake\static_cxx_runtime_overrides.cmake -D JAVA_HOME="C:/Program Files/Java/jdk1.8.0_162" -G "Visual Studio 14 2015 Win64" C:\Users\Marat\Documents\work\cpp\commons-daemon\src\native\windows\apps
```

## Example for building of generated Visual Studio 2015 project

Example of command line for building release version of generated Visual Studio 2015 project (current directory is the directory where generated Visual Studio 2015 project is located):

```cmd
cmake --build . --config Release
```
 
`prunsrv/Release/prunsrv.exe` file is created if build is successful.