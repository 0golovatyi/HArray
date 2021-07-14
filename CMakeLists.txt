cmake_minimum_required(VERSION 3.10)

project(HArray LANGUAGES CXX)

# tell compiler to use C++14 features (needed for std::unique_ptr and
# some templates syntax)
set(CMAKE_CXX_STANDARD 14)

# create static library
add_library(harray STATIC
  src/HArray_delValueByKey.cpp
  src/HArray_getKeysAndValuesByRange.cpp
  src/HArray_getValueByKey.cpp
  src/HArray_hasPartKey.cpp
  src/HArray_insert.cpp
  src/HArray_insertOrGet.cpp
  src/HArray_rebuild.cpp
  src/HArray_scanKeysAndValues.cpp
  src/HArray_shrink.cpp
  src/HArray_test.cpp
  src/HArrayGeneric.cpp)

# prepare for publishin the library
add_library(HArray::harray ALIAS harray)
include(GNUInstallDirs)
install(
  TARGETS harray
  EXPORT HArrayTarget
  ARCHIVE DESTINATION ${CMAKE_INSTALL_DIR})

# make headers discoverable
target_include_directories(
  harray PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/harray>
    $<INSTALL_INTERFACE:harray>)

# build benchmark app
add_executable(HArrayBenchmark benchmark/HArrayBenchmark.cpp)
target_link_libraries(HArrayBenchmark harray)

# export library
install(
  EXPORT HArrayTarget
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/HArray
  NAMESPACE HArray::
  FILE HArrayTargets.cmake
  )

include(CMakePackageConfigHelpers)
configure_package_config_file( 
  "Config.cmake.in" 
  "HArrayConfig.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/HArray
  PATH_VARS
    CMAKE_INSTALL_LIBDIR
  )

write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/HArrayConfigVersion.cmake
  VERSION 1.0.0
  COMPATIBILITY SameMajorVersion
  )

### Install Config and ConfigVersion files
install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/HArryaConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/HArrayConfigVersion.cmake"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/HArray"
  )
