# CMakeLists.txt
#
#     Author: Fabian Meyer
# Created On: 26 Dec 2015
#    License: MIT

cmake_minimum_required(VERSION 3.15)

project(inifile-cpp LANGUAGES CXX)

include(CTest)

option(GENERATE_COVERAGE "Enable generating code coverage" OFF)
option(BUILD_TESTS "Enable building unit tests" OFF)
option(BUILD_EXAMPLES "Enable building example applications" OFF)

add_subdirectory(dep)

add_library(inicpp INTERFACE)
target_include_directories(inicpp INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)
add_library(inicpp::inicpp ALIAS inicpp)
target_compile_features(inicpp INTERFACE cxx_std_11)

target_compile_options(inicpp INTERFACE
    $<$<CXX_COMPILER_ID:GNU,Clang>:-Wall;-Wextra>
    $<$<CXX_COMPILER_ID:MSVC>:/WX;/wd4530>
)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/include/inicpp.h TYPE INCLUDE)

if(GENERATE_COVERAGE AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    # Add required flags (GCC & LLVM/Clang)
    target_compile_options(inicpp INTERFACE -O0 -g --coverage)
    target_link_options(inicpp INTERFACE --coverage)
endif(GENERATE_COVERAGE AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")

if(${BUILD_TESTS})
    add_subdirectory(test)
endif(${BUILD_TESTS})

if(${BUILD_EXAMPLES})
    add_subdirectory(examples)
endif(${BUILD_EXAMPLES})
