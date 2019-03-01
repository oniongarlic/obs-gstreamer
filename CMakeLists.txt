cmake_minimum_required(VERSION 2.8)
project(obs-gstreamer)
set(obs-gstreamer_DESCRIPTION "Allows connect DSLR cameras with obs-studio through gstreamer on Linux.")
set(obs-gstreamer_VERSION_MAJOR 0)
set(obs-gstreamer_VERSION_MINOR 3)
set(obs-gstreamer_VERSION_PATCH 0)
set(CMAKE_BUILD_TYPE Release)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/modules/")

find_package(LibObs)
if(${LibObs_FOUND})
    message(STATUS "LibObs FOUND")
else()
    message(FATAL_ERROR "LibObs NOT FOUND")
endif()

find_package(PkgConfig)

pkg_check_modules(gstreamer-1.0 gstreamer-video-1.0 gstreamer-audio-1.0 gstreamer-app-1.0)

math(EXPR BITS "8*${CMAKE_SIZEOF_VOID_P}")
set(PLUGIN_DIRECTORY "${CMAKE_BINARY_DIR}/build/obs-gstreamer")
set(PLUGIN_BIN_DIRECTORY "${PLUGIN_DIRECTORY}/bin/${BITS}bit")

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PLUGIN_BIN_DIRECTORY})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PLUGIN_BIN_DIRECTORY})

include_directories(src ${LIBOBS_INCLUDE_DIRS} ${gstreamer-1.0_INCLUDE_DIRS} ${gstreamer-app-1.0_INCLUDE_DIRS})

set(SOURCE_FILES gstreamer.c)

add_library(obs-gstreamer MODULE ${SOURCE_FILES})

SET_TARGET_PROPERTIES(obs-gstreamer PROPERTIES PREFIX "")
target_link_libraries(obs-gstreamer ${LIBOBS_LIBRARIES} ${gstreamer-1.0_LIBRARIES} ${gstreamer-app-1.0_LIBRARIES})

# install
if(${SYSTEM_INSTALL})
    install(TARGETS obs-gstreamer DESTINATION /usr/lib/obs-plugins)
else()
    install(DIRECTORY ${PLUGIN_DIRECTORY} DESTINATION $ENV{HOME}/.config/obs-studio/plugins USE_SOURCE_PERMISSIONS)
endif()
