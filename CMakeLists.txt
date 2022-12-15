cmake_minimum_required(VERSION 3.11.4)
project(Maestro)

option(PYTHON_BINDING "Set when you want to build PYTHON_BINDING (Python bindings for the library)" OFF)

include(FindPkgConfig)
pkg_search_module(LIBUSB REQUIRED libusb-1.0)
include_directories(${LIBUSB_INCLUDE_DIRS})

# Extract version information from git
find_package(Git REQUIRED)
execute_process(
  COMMAND ${GIT_EXECUTABLE} describe --always --tags
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE MAESTRO_VERSION
  ERROR_QUIET
  OUTPUT_STRIP_TRAILING_WHITESPACE
)
message(STATUS "VERSION: ${MAESTRO_VERSION}")

add_subdirectory(src)
add_subdirectory(firmwares)

if(PYTHON_BINDING)
    add_subdirectory(python)
endif()

# Package builder
set(CPACK_PACKAGE_NAME "Maestro")
set(CPACK_PACKAGE_VENDOR "https://github.com/papabricole/Pololu-Maestro")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "A cross-platform C++ library for controlling Pololu Maestro devices")
set(CPACK_PACKAGE_VERSION ${MAESTRO_VERSION})
set(CPACK_PACKAGE_INSTALL_DIRECTORY "${CMAKE_PROJECT_NAME}")
set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/LICENSE")

include(CPack)
