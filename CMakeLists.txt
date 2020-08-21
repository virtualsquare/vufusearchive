cmake_minimum_required(VERSION 3.12)
project(vufusearchive
    VERSION 0.9.1
    DESCRIPTION "VUOS/vufuse module for archives tar/tgz/.../iso9660"
    HOMEPAGE_URL "https://github.com/virtualsquare/vufusearchive"
    LANGUAGES C)

include(GNUInstallDirs)
include(CheckIncludeFile)
FIND_PATH (FUSE_INCLUDE_DIR fuse_opt.h
        /usr/local/include
        /usr/include
        /usr/include/fuse
        )
include_directories(${CMAKE_CURRENT_SOURCE_DIR} 
		${CMAKE_CURRENT_BINARY_DIR} ${FUSE_INCLUDE_DIR})

# check availability of headers and libs
set(HEADERS_REQUIRED fuse.h)
set(LIBS_REQUIRED fuse archive)

set(CMAKE_REQUIRED_DEFINITIONS -D_FILE_OFFSET_BITS=64)
configure_file(config.h.in config.h @ONLY)

foreach(HEADER IN LISTS HEADERS_REQUIRED)
  check_include_file(${HEADER} ${HEADER}_OK)
  if(NOT ${HEADER}_OK)
    message(FATAL_ERROR "header file ${HEADER} not found")
  endif()
endforeach(HEADER)

foreach(THISLIB IN LISTS LIBS_REQUIRED)
  find_library(LIB${THISLIB}_OK ${THISLIB})
  if(NOT LIB${THISLIB}_OK)
    message(FATAL_ERROR "library lib${THISLIB} not found")
  endif()
endforeach(THISLIB)

# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -D_FORTIFY_SOURCE=2 -O2 -pedantic -Wall -Wextra")
# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -pedantic -Wall -Wextra")

add_definitions(-D_GNU_SOURCE -D_FILE_OFFSET_BITS=64)

add_library(vufusearchive SHARED archivemount.c)
target_link_libraries(vufusearchive archive)
set_target_properties(vufusearchive PROPERTIES PREFIX "")
install(TARGETS vufusearchive
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/vu/modules)

# add_subdirectory(man)

add_custom_target(uninstall
  "${CMAKE_COMMAND}"
    -DCADO_SPOOL_DIR=${CADO_SPOOL_DIR}
    -P "${PROJECT_SOURCE_DIR}/Uninstall.cmake"
  )

