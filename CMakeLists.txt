cmake_minimum_required(VERSION 3.12)
project(vufusearchive
    VERSION 0.9.1
    DESCRIPTION "VUOS/vufuse module for archives tar/tgz/.../iso9660"
    HOMEPAGE_URL "https://github.com/virtualsquare/vufusearchive"
    LANGUAGES CXX)

include(GNUInstallDirs)
include(CheckIncludeFile)
include_directories(${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_BINARY_DIR})
#execute_process(COMMAND "sh -c 'cd archivemount-ng; git describe'"
#		OUTPUT_VARIABLE ARCHIVEMOUNT_NG_VERSION)
execute_process(COMMAND git describe
		WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/archivemount-ng
		OUTPUT_STRIP_TRAILING_WHITESPACE
		OUTPUT_VARIABLE ARCHIVEMOUNT_NG_VERSION)

find_package(PkgConfig)
pkg_check_modules(FUSE fuse3)
if(NOT FUSE_FOUND)
  pkg_check_modules(FUSE REQUIRED fuse)
endif()
string(REGEX REPLACE "\\..*" "" FUSE_VERSION ${FUSE_VERSION})

set(CMAKE_REQUIRED_DEFINITIONS -D_FILE_OFFSET_BITS=64)

configure_file(config.h.in config.h @ONLY)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_FORTIFY_SOURCE=2 -O2 -pedantic -Wall -Wextra")

add_definitions(-D_GNU_SOURCE -D_FILE_OFFSET_BITS=64 ${FUSE_CFLAGS} -DFUSE=${FUSE_VERSION}
		-DVERSION="${ARCHIVEMOUNT_NG_VERSION}")

add_library(vufusearchive SHARED archivemount.cpp)
target_link_libraries(vufusearchive archive)
set_target_properties(vufusearchive PROPERTIES PREFIX "")
install(TARGETS vufusearchive
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/vu/modules)

# add_subdirectory(man)

add_custom_target(uninstall
  "${CMAKE_COMMAND}"
    -P "${PROJECT_SOURCE_DIR}/Uninstall.cmake"
  )

