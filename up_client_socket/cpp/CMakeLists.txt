# SPDX-FileCopyrightText: 2024 Contributors to the Eclipse Foundation
#
# See the NOTICE file(s) distributed with this work for additional
# information regarding copyright ownership.
#
# This program and the accompanying materials are made available under the
# terms of the Apache License Version 2.0 which is available at
# https://www.apache.org/licenses/LICENSE-2.0
#
# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.20.1)
project(up-transport-socket-cpp VERSION 0.1.0  LANGUAGES CXX DESCRIPTION "C++ socket transport")

find_package(up-cpp REQUIRED)
find_package(protobuf REQUIRED)
find_package(up-core-api REQUIRED)
find_package(spdlog REQUIRED)
add_definitions(-DSPDLOG_FMT_EXTERNAL)
find_package(fmt REQUIRED CONFIG)

# This is the root CMakeLists.txt file; We can set project wide settings here
if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
    set(CMAKE_CXX_STANDARD 17)
    # place libraries in a lib directory and executables in a bin directory,
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
endif()

add_library(${PROJECT_NAME} src/SocketUTransport.cpp)

add_library(up-transport-socket-cpp::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_include_directories(${PROJECT_NAME}
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
    $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}> 
    ${up-cpp_INCLUDE_DIR}
    ${up-core-api_INCLUDE_DIR}
    ${protobuf_INCLUDE_DIR}
    ${spdlog_INCLUDE_DIR})

set_property(TARGET ${PROJECT_NAME} PROPERTY POSITION_INDEPENDENT_CODE ON)

target_link_libraries(${PROJECT_NAME} 
    PRIVATE
    pthread
    rt
    dl 
    spdlog::spdlog
    up-cpp::up-cpp
    up-core-api::up-core-api
    protobuf::libprotobuf)

add_executable(myTest src/test.cpp)
target_include_directories(myTest
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
    $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}> 
    ${up-cpp_INCLUDE_DIR}
    ${up-core-api_INCLUDE_DIR}
    ${protobuf_INCLUDE_DIR}
    ${spdlog_INCLUDE_DIR})
target_link_libraries(myTest ${PROJECT_NAME} spdlog::spdlog)

# Specify the install location for the library
INSTALL(TARGETS ${PROJECT_NAME})
INSTALL(DIRECTORY include DESTINATION .)

