# CMakeLists.txt
#
#     Author: Fabian Meyer
# Created On: 26 Dec 2015
#    License: MIT

cmake_minimum_required(VERSION 3.15)

project(inifile-cpp LANGUAGES CXX)
include(GNUInstallDirs)

add_library(inicpp INTERFACE)
add_library(inicpp::inicpp ALIAS inicpp)
target_compile_features(inicpp INTERFACE cxx_std_11)
target_include_directories(inicpp INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

if(NOT DEFINED INIFILE_CPP_MASTER_PROJECT)
    if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
        set(INIFILE_CPP_MASTER_PROJECT ON)
    else()
        set(INIFILE_CPP_MASTER_PROJECT OFF)
    endif()
endif()

if(INIFILE_CPP_MASTER_PROJECT)
    target_compile_options(inicpp INTERFACE
        $<$<CXX_COMPILER_ID:GNU,Clang>:-Wall;-Wextra>
        $<$<CXX_COMPILER_ID:MSVC>:/W4>
    )
endif()

option(GENERATE_COVERAGE "Enable generating code coverage" ${INIFILE_CPP_MASTER_PROJECT})
option(BUILD_TESTS "Enable building unit tests" ${INIFILE_CPP_MASTER_PROJECT})
option(BUILD_EXAMPLES "Enable building example applications" ${INIFILE_CPP_MASTER_PROJECT})

install(DIRECTORY include/
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
        FILES_MATCHING
        PATTERN "*.h"
)

if(GENERATE_COVERAGE AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    # Add required flags (GCC & LLVM/Clang)
    target_compile_options(inicpp INTERFACE -O0 -g --coverage)
    target_link_options(inicpp INTERFACE --coverage)
endif()

if(BUILD_TESTS)
    message(STATUS "[inicpp] Building unit tests")
    include(CTest)
    add_subdirectory(dep)
    add_subdirectory(test)
endif()

if(BUILD_EXAMPLES)
    message(STATUS "[inicpp] Building examples")
    add_subdirectory(examples)
endif()
