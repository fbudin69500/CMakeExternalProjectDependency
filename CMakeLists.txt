cmake_minimum_required(VERSION 3.0)

option(Superbuild "Build Superbuild" ON)

if(NOT Superbuild)
  add_library(test_library STATIC library.cxx)
  return()
endif()

include(ExternalProject)

set(MyExternalProject external_project_library)
  ExternalProject_Add(${MyExternalProject}
    DOWNLOAD_COMMAND ""
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    BINARY_DIR ${MyExternalProject}-build
    INSTALL_COMMAND ""
   CMAKE_ARGS
    -DSuperbuild:BOOL=OFF
    )

link_directories(${CMAKE_CURRENT_BINARY_DIR}/external_project_library-build)
add_library(test_library STATIC IMPORTED)
set_property(TARGET test_library PROPERTY IMPORTED_LOCATION
   ${CMAKE_CURRENT_BINARY_DIR}/${MyExternalProject}-build/libtest_library.a)
add_dependencies(test_library ${MyExternalProject})
add_executable(test_executable executable.cxx)

target_link_libraries(test_executable test_library)
