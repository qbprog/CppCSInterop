cmake_minimum_required(VERSION 3.25)
project(CppCSInterop)

# Latest SDK, customize it
set(CMAKE_SYSTEM_VERSION 10.0.26100.0)

# Copy the build workarounds
configure_file("${CMAKE_SOURCE_DIR}/Directory.Build.props" "${CMAKE_BINARY_DIR}/Directory.Build.props")
configure_file("${CMAKE_SOURCE_DIR}/Directory.Build.targets" "${CMAKE_BINARY_DIR}/Directory.Build.targets")

# Downloads the nuget command line tool
if (NOT EXISTS ${CMAKE_BINARY_DIR}/tools/nuget.exe)
  message("Downloading nuget.exe")
  file(DOWNLOAD https://dist.nuget.org/win-x86-commandline/latest/nuget.exe ${CMAKE_BINARY_DIR}/tools/nuget.exe SHOW_PROGRESS )
endif()
set(NUGET_EXE "${CMAKE_BINARY_DIR}/tools/nuget.exe")

# Downloads the UndocketWinRT package from github (sources)
if (NOT EXISTS ${CMAKE_BINARY_DIR}/download/UndockedWinRT.zip)
	message("Downloading xlang")
	file(DOWNLOAD https://github.com/microsoft/xlang/archive/refs/heads/master.zip ${CMAKE_BINARY_DIR}/download/UndockedWinRT.zip SHOW_PROGRESS )
	file(ARCHIVE_EXTRACT INPUT ${CMAKE_BINARY_DIR}/download/UndockedWinRT.zip DESTINATION ${CMAKE_BINARY_DIR}/download/)
	file(GLOB DETOURS_FILES "${CMAKE_BINARY_DIR}/download/xlang-master/src/UndockedRegFreeWinRT/detours/*.*")
	file(COPY ${DETOURS_FILES} DESTINATION ${CMAKE_SOURCE_DIR}/detours/)

	file(GLOB UNDRFWR_FILES "${CMAKE_BINARY_DIR}/download/xlang-master/src/UndockedRegFreeWinRT/UndockedRegFreeWinRT/*.*")
	file(COPY ${UNDRFWR_FILES} DESTINATION ${CMAKE_SOURCE_DIR}/UndockedRegFreeWinRT/)
endif()

# Downloads the required cpp nuget packages
if (NOT EXISTS "${CMAKE_BINARY_DIR}/packages/")
   exec_program(${NUGET_EXE} ARGS install "Microsoft.Windows.CppWinRT" -Version 2.0.250303.1 -ExcludeVersion -OutputDirectory ${CMAKE_BINARY_DIR}/packages)
   exec_program(${NUGET_EXE} ARGS install "Microsoft.Windows.ImplementationLibrary" -Version 1.0.260126.7 -ExcludeVersion -OutputDirectory ${CMAKE_BINARY_DIR}/packages)
endif()

# Post-configure event: copy the c++ redist to our distrib directory
include(InstallRequiredSystemLibraries)
file(GLOB REDIST_FILES "${MSVC_REDIST_DIR}/x64/Microsoft.VC143.CRT/*.*")
file(COPY ${REDIST_FILES} DESTINATION ${CMAKE_SOURCE_DIR}/distrib/)

# C++ winrt component 
add_subdirectory(CppComponent)

# C# projection of the C# component
include_external_msproject(CppComponentProjection "${CMAKE_CURRENT_SOURCE_DIR}/CppComponentProjection/CppComponentProjection.csproj" PLATFORM "x64")

# C# EXE example to use the CppComponent and it's projection
include_external_msproject(CSTestEXE "${CMAKE_CURRENT_SOURCE_DIR}/CSTestEXE/CSTestEXE.csproj" PLATFORM "x64")

# C# winrt component
include_external_msproject(CSComponent "${CMAKE_CURRENT_SOURCE_DIR}/CSComponent/CSComponent.csproj" PLATFORM "x64")

# C++ example of usage of such C# component, in form of a DLL
add_subdirectory(CSComponentClient)

# C++ EXE using the CSComponentClient DLL
add_subdirectory(CppTestEXE)

# Support for Windows 1607 using the detours and UndocketRegFreeWinRT
add_subdirectory(detours)
add_subdirectory(UndockedRegFreeWinRT)



