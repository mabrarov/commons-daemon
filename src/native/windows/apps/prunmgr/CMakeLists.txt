cmake_minimum_required(VERSION 3.4)
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
set(manifest_files )
set(link_options )
set(libraries )

list(APPEND cxx_compile_definitions
    "WIN32"
    "_WIN32"
    "_WINDOWS"
    "_WINNT"
    "_WIN32_WINNT=${cmdm_nmake_winver}"
    "WINVER=${cmdm_nmake_winver}"
    "_WIN32_IE=${cmdm_win32_ie}"
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
        "/W3"
        "/TC"
        "/EHsc"
        "/Zi"
        "$<$<NOT:$<CONFIG:DEBUG>>:/Ob2>"
        "$<$<CONFIG:DEBUG>:/RTC1>")
    if(NOT (CMAKE_SIZEOF_VOID_P EQUAL 8))
        list(APPEND cxx_compile_options
            "$<$<NOT:$<CONFIG:DEBUG>>:/Oy->")
    endif()
    if((MSVC_VERSION EQUAL 1900) OR (MSVC_VERSION GREATER 1900))
        # Turn off option which is not turned on by default in command line
        list(APPEND cxx_compile_options
            "/Zc:inline-")
    endif()
endif()

if(MSVC)
    list(APPEND resource_compile_options
        "/l 0x409")
endif()

list(APPEND cxx_headers
    "${cmdm_native_windows_include_dir}/apxwin.h"
    "${cmdm_native_windows_include_dir}/cmdline.h"
    "${cmdm_native_windows_include_dir}/console.h"
    "${cmdm_native_windows_include_dir}/gui.h"
    "${cmdm_native_windows_include_dir}/handles.h"
    "${cmdm_native_windows_include_dir}/javajni.h"
    "${cmdm_native_windows_include_dir}/log.h"
    "${cmdm_native_windows_include_dir}/registry.h"
    "${cmdm_native_windows_include_dir}/rprocess.h"
    "${cmdm_native_windows_include_dir}/service.h"
    "${cmdm_native_windows_src_dir}/mclib.h"
    "${cmdm_native_windows_src_dir}/private.h")

list(APPEND cxx_sources
    "${cmdm_native_windows_src_dir}/cmdline.c"
    "${cmdm_native_windows_src_dir}/console.c"
    "${cmdm_native_windows_src_dir}/gui.c"
    "${cmdm_native_windows_src_dir}/handles.c"
    "${cmdm_native_windows_src_dir}/javajni.c"
    "${cmdm_native_windows_src_dir}/log.c"
    "${cmdm_native_windows_src_dir}/mclib.c"
    "${cmdm_native_windows_src_dir}/registry.c"
    "${cmdm_native_windows_src_dir}/rprocess.c"
    "${cmdm_native_windows_src_dir}/service.c"
    "${cmdm_native_windows_src_dir}/utils.c"
    "${cmdm_native_windows_apps_dir}/prunmgr/prunmgr.c")

list(APPEND resource_headers
    "${cmdm_native_windows_apps_dir}/prunmgr/prunmgr.h")

list(APPEND resource_sources
    "${cmdm_native_windows_apps_dir}/prunmgr/prunmgr.rc")

list(APPEND manifest_files
    "${cmdm_native_windows_apps_dir}/prunmgr/prunmgr.manifest")

set(resource_files
    ${resource_headers}
    ${resource_sources}
    ${manifest_files})

set_property(SOURCE ${resource_sources} APPEND PROPERTY COMPILE_FLAGS ${resource_compile_options})

if(MSVC)
    list(APPEND link_options
        "/version:1.0"
        "/DYNAMICBASE"
        "/NXCOMPAT"
        "/DEBUG"
        "/INCREMENTAL:NO"
        "/OPT:REF"
        "/MANIFEST:NO")
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
    ${resource_sources}
    ${manifest_files})
target_compile_options(${target_name}
    PRIVATE
    ${cxx_compile_options})
target_compile_definitions(${target_name}
    PRIVATE
    ${cxx_compile_definitions})
target_include_directories(${target_name}
    PUBLIC
    ${cmdm_native_windows_include_dir}
    ${cmdm_native_windows_src_dir}
    ${JNI_INCLUDE_DIRS})

set(link_option_str "")
if(NOT MSVC)
    # This should work for most of CMake generators except Visual Studio
    cmdm_list_to_string("${link_options}" "," link_option_str)
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
#cmdm_set_target_output_dir(${target_name} "${cmdm_target_dir}")
set_target_properties(${target_name} PROPERTIES OUTPUT_NAME "${output_name}")

if(NOT cmdm_no_cmake_dir_source_group)
    # Group files according to file path
    cmdm_dir_source_group("Header Files" "${cmdm_native_windows_dir}" "${cxx_headers}")
    cmdm_dir_source_group("Source Files" "${cmdm_native_windows_dir}" "${cxx_sources}")
    cmdm_dir_source_group("Resource Files" "${cmdm_native_windows_dir}" "${resource_files}")
endif()
