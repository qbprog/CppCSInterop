# CppCSInterop

 C++ C# Interop example using WinRT

 See blog post [C++/C# interop for native applications using WinRT | QbProg's C++ and Geometry blog](https://qbprog.github.io/2023/02/16/cc-interop-for-native-applications-using-winrt.html)

# Requirements

- Visual Studio 2026
- CMake
- (Windows SDK 10.0.26100.0)

Note that the Windows SDK should be matching exactly the specific version. In case replace the version specified in the csproj files with your own.

# Instructions

    setup.bat

# Build

    cmake --build build

# Run

    cd distrib
    CppTextEXE.exe
