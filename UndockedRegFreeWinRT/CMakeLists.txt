PROJECT(UndockedRegFreeWinRT)

SET(SOURCES catalog.cpp
			catalog.h
			dllmain.cpp
			typeresolution.cpp
			typeresolution.h
			winrtact.def
			packages.config
	)

add_definitions(/FI"xstring")
ADD_LIBRARY(winrtact SHARED ${SOURCES})

target_compile_definitions(winrtact PRIVATE "-D_UNICODE" "-DUNDOCKEDREGFREEWINRT_EXPORTS" "-D_USRDLL")
set_target_properties(winrtact PROPERTIES CXX_STANDARD 17 CXX_STANDARD_REQUIRED ON)
set_property(TARGET winrtact PROPERTY VS_PACKAGE_REFERENCES "Microsoft.Windows.ImplementationLibrary_1.0.260126.7")

target_include_directories(winrtact PRIVATE "${CMAKE_BINARY_DIR}/packages/Microsoft.Windows.ImplementationLibrary/include/")

target_link_libraries(winrtact PRIVATE detours)
target_link_libraries(winrtact PRIVATE comsuppw.lib shlwapi.lib xmllite.lib runtimeobject.lib Pathcch.lib Rometadata.lib)
target_link_libraries(winrtact PRIVATE ${CMAKE_BINARY_DIR}/packages/Microsoft.Windows.ImplementationLibrary/build/native/Microsoft.Windows.ImplementationLibrary.targets)

# Copies the file to the destination directory
add_custom_command(TARGET winrtact POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:winrtact> ${CMAKE_SOURCE_DIR}/distrib/)