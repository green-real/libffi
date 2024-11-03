cmake_minimum_required(VERSION 3.10)
project(libffi C)

set(SOURCES_LIST
    src/closures.c
    src/java_raw_api.c
    src/prep_cif.c
    src/raw_api.c
    src/types.c)

if(CMAKE_BUILD_TYPE MATCHES Debug)
    list(APPEND SOURCES_LIST src/debug.c)
    add_definitions(-DFFI_DEBUG)
endif()

include(configure_platform.cmake)
include(configure_options.cmake)

file(COPY ${FFI_CONFIG_FILE} DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
file(COPY src/${TARGETDIR}/ffitarget.h DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/include)

include_directories(${CMAKE_CURRENT_BINARY_DIR}/include)
include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(include)

add_definitions(-DFFI_BUILDING)

add_library(objlib OBJECT ${SOURCES_LIST})
set_property(TARGET objlib PROPERTY POSITION_INDEPENDENT_CODE 1)

add_library(libffi STATIC $<TARGET_OBJECTS:objlib>)

target_include_directories(
  libffi PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>")

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
