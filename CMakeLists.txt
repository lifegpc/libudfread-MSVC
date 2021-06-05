cmake_minimum_required(VERSION 3.0)

set(UDFREAD_VERSION_MAJOR 1)
set(UDFREAD_VERSION_MINOR 1)
set(UDFREAD_VERSION_MICRO 2)
set(UDFREAD_VERSION ${UDFREAD_VERSION_MAJOR}.${UDFREAD_VERSION_MINOR}.${UDFREAD_VERSION_MICRO})

project(libudfread VERSION ${UDFREAD_VERSION})

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")
include(CheckLargeFile)
GMX_TEST_LARGE_FILES(LARGE_FILES)

include(CheckIncludeFile)
CHECK_INCLUDE_FILE(dlfcn.h HAVE_DLFCN_H)
CHECK_INCLUDE_FILE(unistd.h HAVE_UNISTD_H)
CHECK_INCLUDE_FILE(pthread.h HAVE_PTHREAD_H)

configure_file(cmake_config.h.in config.h)
configure_file(src/udfread-version.h.in udfread-version.h)
if (MSVC) 
    configure_file(version.rc.in ${CMAKE_SOURCE_DIR}/version.rc)
endif()
include_directories(${CMAKE_CURRENT_BINARY_DIR})

add_definitions(-DHAVE_CONFIG_H)
set(OBJS src/default_blockinput.c src/ecma167.c src/udfread.c src/udfread-version.c)
set(SHARED_OBJS ${OBJS})
if (MSVC)
    list(APPEND SHARED_OBJS version.rc)
endif()
add_library(libudfread SHARED ${SHARED_OBJS})
set_target_properties(libudfread PROPERTIES
PUBLIC_HEADER "${CMAKE_SOURCE_DIR}/src/udfread.h;${CMAKE_SOURCE_DIR}/src/blockinput.h;${CMAKE_BINARY_DIR}/udfread-version.h"
)
add_library(libudfread-static STATIC ${OBJS})
install(TARGETS libudfread libudfread-static)
