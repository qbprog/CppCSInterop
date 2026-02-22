project(CSComponentClient)

# Common windows definitions
add_definitions(-D_UNICODE -D _WINRT_DLL /EHsc)

# adds a library. It contains the custom WinRT activation
add_library(CSComponentClient SHARED dllmain.cpp packages.config WinRTCustomActivation.h WinRTCustomActivation.cpp)

# links cppwinrt
set_target_properties(CSComponentClient PROPERTIES VS_PROJECT_IMPORT ${CMAKE_BINARY_DIR}/packages/Microsoft.Windows.CppWinRT/build/native/Microsoft.Windows.CppWinRT.props)
target_link_libraries(CSComponentClient PRIVATE ${CMAKE_BINARY_DIR}/packages/Microsoft.Windows.CppWinRT/build/native/Microsoft.Windows.CppWinRT.targets)

# various c++ properties
set_target_properties(CSComponentClient PROPERTIES CXX_STANDARD 20 CXX_STANDARD_REQUIRED ON CXX_EXTENSIONS OFF)
set_target_properties(CSComponentClient PROPERTIES VS_WINDOWS_TARGET_PLATFORM_MIN_VERSION 10.0.14393.0)

# depends on the C# project to get a correct build order
add_dependencies(CSComponentClient CSComponent)

# copies the artifacts
add_custom_command(TARGET CSComponentClient POST_BUILD COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_SOURCE_DIR}/distrib/)
add_custom_command(TARGET CSComponentClient POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:CSComponentClient> ${CMAKE_SOURCE_DIR}/distrib/)