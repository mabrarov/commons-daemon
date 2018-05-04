cmake_minimum_required(VERSION 3.0)
project(prunmgr)

find_package(JNI REQUIRED)

set(target_name "${PROJECT_NAME}")
set(output_name "prunmgr")

set(cxx_compile_definitions )
set(cxx_compile_options )
set(resource_compile_options )
set(cxx_headers )
set(cxx_sources )
set(resource_headers )
set(resource_sources )
set(link_options )
set(libraries )

list(APPEND cxx_compile_definitions
    "WIN32"
    "_WIN32"
    "_WINDOWS"
    "_WINNT"
    "_WIN32_WINNT=0x0700"
    "WINVER=0x0700"
    "_WIN32_IE=0x0800"
    "_UNICODE"
    "UNICODE"
    "_CRT_SECURE_NO_WARNINGS")

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    list(APPEND cxx_compile_definitions
        "_AMD64_=1"
        "WIN64"
        "_WIN64")
else()
    list(APPEND cxx_compile_definitions
        "_X86_=1")
endif()

if(MSVC)
    list(APPEND cxx_compile_options
        "/TC"
        "/EHsc"
        "/Zi"
        "$<$<NOT:$<CONFIG:DEBUG>>:/Ob2>"
        "/W3"
        "/GS"
        "/Zc:wchar_t"
        "/Zc:inline"
        "/Zc:forScope"
        "/fp:precise"
        "/Gd")
    if(NOT (CMAKE_SIZEOF_VOID_P EQUAL 8))
        list(APPEND cxx_compile_options
            "$<$<NOT:$<CONFIG:DEBUG>>:/Oy->")
    endif()
endif()

if(MSVC)
    list(APPEND resource_compile_options
        "/l 0x419")
endif()

list(APPEND cxx_headers
    "${dm_native_windows_include_dir}/apxwin.h"
    "${dm_native_windows_include_dir}/cmdline.h"
    "${dm_native_windows_include_dir}/console.h"
    "${dm_native_windows_include_dir}/gui.h"
    "${dm_native_windows_include_dir}/handles.h"
    "${dm_native_windows_include_dir}/javajni.h"
    "${dm_native_windows_include_dir}/log.h"
    "${dm_native_windows_include_dir}/registry.h"
    "${dm_native_windows_include_dir}/rprocess.h"
    "${dm_native_windows_include_dir}/service.h"
    "${dm_native_windows_src_dir}/mclib.h"
    "${dm_native_windows_src_dir}/private.h")

list(APPEND cxx_sources
    "${dm_native_windows_src_dir}/cmdline.c"
    "${dm_native_windows_src_dir}/console.c"
    "${dm_native_windows_src_dir}/gui.c"
    "${dm_native_windows_src_dir}/handles.c"
    "${dm_native_windows_src_dir}/javajni.c"
    "${dm_native_windows_src_dir}/log.c"
    "${dm_native_windows_src_dir}/mclib.c"
    "${dm_native_windows_src_dir}/registry.c"
    "${dm_native_windows_src_dir}/rprocess.c"
    "${dm_native_windows_src_dir}/service.c"
    "${dm_native_windows_src_dir}/utils.c"
    "${dm_native_windows_apps_dir}/prunmgr/prunmgr.c")

list(APPEND resource_headers
    "${dm_native_windows_apps_dir}/prunmgr/prunmgr.h")

list(APPEND resource_sources
    "${dm_native_windows_apps_dir}/prunmgr/prunmgr.rc")

set(resource_files
    ${resource_headers}
    ${resource_sources})

set_property(SOURCE ${resource_sources} APPEND PROPERTY COMPILE_FLAGS ${resource_compile_options})

if(MSVC)
    list(APPEND link_options
        "/version:1.0"
        "/DYNAMICBASE"
        "/NXCOMPAT"
        "/DEBUG"
        "/INCREMENTAL:NO"
        "/OPT:REF")
endif()

list(APPEND libraries
    "kernel32"
    "advapi32"
    "ws2_32"
    "mswsock"
    "ole32"
    "shell32"
    "rpcrt4"
    "user32"
    "gdi32"
    "winspool"
    "comdlg32"
    "comctl32"
    "shlwapi"
    "netapi32")

add_executable(${target_name} WIN32
    ${cxx_headers}
    ${cxx_sources}
    ${resource_headers}
    ${resource_sources})
target_compile_options(${target_name}
    PRIVATE
    ${cxx_compile_options})
target_compile_definitions(${target_name}
    PRIVATE
    ${cxx_compile_definitions})
target_include_directories(${target_name}
    PUBLIC
    ${dm_native_windows_include_dir}
    ${dm_native_windows_src_dir}
    ${JNI_INCLUDE_DIRS})

set(link_option_str "")
if(NOT MSVC)
    # This should work for most of CMake generators except Visual Studio
    dm_list_to_string("${link_options}" "," link_option_str)
endif()

target_link_libraries(${target_name}
    PRIVATE
    "${link_option_str}"
    ${libraries})

if(MSVC)
    foreach(link_option IN LISTS link_options)
        # This is the only way I found to pass linker options using Visual Studio CMake generator
        set_property(TARGET ${target_name} APPEND_STRING PROPERTY LINK_FLAGS " ${link_option}")
    endforeach()
endif()

# Do not put results of build to the same place as makefiles do
# because "CMake style" prefers out-of-source tree builds.
#dm_set_target_output_dir(${target_name} "${dm_target_dir}")
set_target_properties(${target_name} PROPERTIES OUTPUT_NAME "${output_name}")

if(NOT dm_no_cmake_dir_source_group)
    # Group files according to file path
    dm_dir_source_group("Header Files" "${dm_native_windows_dir}" "${cxx_headers}")
    dm_dir_source_group("Source Files" "${dm_native_windows_dir}" "${cxx_sources}")
    dm_dir_source_group("Resource Files" "${dm_native_windows_dir}" "${resource_files}")
endif()