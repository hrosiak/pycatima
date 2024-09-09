# BSD 3-Clause License; see https://github.com/scikit-hep/awkward-1.0/blob/main/LICENSE

cmake_minimum_required(VERSION 3.15...3.24)

project(
  ${SKBUILD_PROJECT_NAME}
  VERSION ${SKBUILD_PROJECT_VERSION}
  LANGUAGES CXX
  )

message(STATUS "CMake version ${CMAKE_VERSION}")
message(STATUS "CMAKE_BUILD_TYPE = ${CMAKE_BUILD_TYPE}")

# Defaults for properties in this directory (and below)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Build a static library
set(BUILD_SHARED_LIBS OFF)

# Hide all unused symbols
set(CMAKE_CXX_VISIBILITY_PRESET hidden)
set(CMAKE_VISIBILITY_INLINES_HIDDEN ON)

# Add main library (TODO: use zip, not git clone)
include(FetchContent)
FetchContent_Declare(
  catima
  URL https://github.com/hrosiak/catima/archive/refs/tags/v1.90.zip
  SOURCE_SUBDIR catima-1.90
  )
FetchContent_GetProperties(catima)
if(NOT catima_POPULATED)
  FetchContent_Populate(catima)
  set(APPS OFF)
  add_subdirectory(${catima_SOURCE_DIR} ${catima_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

# Build extension
find_package(pybind11 CONFIG REQUIRED)
pybind11_add_module(_ext src/_ext.cpp)
target_link_libraries(_ext PRIVATE catima)

# Install python bindings
install(
  TARGETS _ext
  LIBRARY DESTINATION "${SKBUILD_PROJECT_NAME}"
  RUNTIME DESTINATION "${SKBUILD_PROJECT_NAME}"
  ARCHIVE DESTINATION "${SKBUILD_PROJECT_NAME}")
