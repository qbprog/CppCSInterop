project(CppTestEXE)

# common windows definitions
add_definitions(-D_UNICODE -D _WINRT_DLL /EHsc)

# add main executable
add_executable(CppTestEXE main.cpp compat.manifest)

#links the DLL which uses the C# component
target_link_libraries(CppTestEXE PRIVATE CSComponentClient)

# forces the winrtact library
IF(TARGET winrtact)
	target_link_libraries(CppTestEXE PRIVATE winrtact)
	target_link_options(CppTestEXE PRIVATE "/INCLUDE:winrtact_Initialize")
endif()

#copies the targets to the destination directory
add_custom_command(TARGET CppTestEXE POST_BUILD COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_SOURCE_DIR}/distrib/)
add_custom_command(TARGET CppTestEXE POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:CppTestEXE> ${CMAKE_SOURCE_DIR}/distrib/)
#add_custom_command(TARGET CppTestEXE POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/CppTestEXE.exe.manifest ${CMAKE_SOURCE_DIR}/distrib/)

# sets the visual studio debugger to use the copied exe when debugging
set_target_properties(CppTestEXE PROPERTIES VS_DEBUGGER_COMMAND "${CMAKE_SOURCE_DIR}/distrib/CppTestEXE.exe")
set_target_properties(CppTestEXE PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}/distrib/")

