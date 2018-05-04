cmake_minimum_required(VERSION 3.0)
project(commons_daemon)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
include(cmake/utils.cmake)

set(dm_home_dir   "${CMAKE_CURRENT_SOURCE_DIR}/../../../..")
set(dm_target_dir "${dm_home_dir}/target")
set(dm_native_dir "${dm_home_dir}/src/native")
set(dm_native_windows_dir         "${dm_native_dir}/windows")
set(dm_native_windows_include_dir "${dm_native_windows_dir}/include")
set(dm_native_windows_src_dir     "${dm_native_windows_dir}/src")
set(dm_native_windows_apps_dir    "${dm_native_windows_dir}/apps")

add_subdirectory(prunsrv)
add_subdirectory(prunmgr)