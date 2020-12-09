# CMakeLists.txt
#
# Copyright 2020 BartekkPL
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not
# use this file except in compliance with the License. You may obtain a copy of
# the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
# License for the specific language governing permissions and limitations under
# the License.
#
# Author
# ~~~
# Bartłomiej Kunikowski, aka BartekkPL, ©BartekkPL 2020

# ------------------------------------------------------------------------------
# INITIALIZATION
# ------------------------------------------------------------------------------

cmake_minimum_required(VERSION 3.13.4)
project(
  vulkan-starter-project
  VERSION 0.0.1
  DESCRIPTION "Vulkan Starter Project"
  LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "-Wall -g")
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

message(STATUS "Start of execution CMake for Vulkan Starter Project in "
               "version ${PROJECT_VERSION}")

# location of additional cmake modules
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

# ------------------------------------------------------------------------------
# EXECUTABLE SOURCES SETTING
# ------------------------------------------------------------------------------

set(${PROJECT_NAME}_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)
set(${PROJECT_NAME}_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

set(${PROJECT_NAME}_SOURCES
    ${${PROJECT_NAME}_SOURCE_DIR}/main.cpp)

add_executable(${PROJECT_NAME} ${${PROJECT_NAME}_SOURCES})

target_include_directories(${PROJECT_NAME}
                           PUBLIC ${${PROJECT_NAME}_INCLUDE_DIR})

set(SHADERS_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/shaders)

# ------------------------------------------------------------------------------
# EXTERNAL MODULES
# ------------------------------------------------------------------------------

# set external modules path
set(THIRD_PARTY_DIR ${PROJECT_SOURCE_DIR}/thirdParty)

# prepare third party libraries
add_subdirectory(${THIRD_PARTY_DIR}/glfw)
add_subdirectory(${THIRD_PARTY_DIR}/glm)
add_subdirectory(${THIRD_PARTY_DIR}/vulkanSDK)

include_directories(
  ${GLFW_INCLUDE_DIRS}
  ${GLM_INCLUDE_DIRS}
  ${VULKAN_SDK_INCLUDE_DIRS}
  )

target_include_directories(${PROJECT_NAME} PRIVATE
  ${GLFW_INCLUDE_DIRS}
  ${GLM_INCLUDE_DIRS}
  ${VULKAN_SDK_INCLUDE_DIRS}
  )

target_link_libraries(${PROJECT_NAME}
  ${GLFW_LIBRARIES}
  ${GLM_LIBRARIES}
  ${VULKAN_SDK_LIBRARIES}
  )

add_dependencies(${PROJECT_NAME} vulkanSDK)
add_dependencies(${PROJECT_NAME} glm)
add_dependencies(${PROJECT_NAME} glfw)

#-------------------------------------------------------------------------------
# GENERATE CONFIGURE FILE
#-------------------------------------------------------------------------------

# configure header file
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.h.in
    ${CMAKE_CURRENT_BINARY_DIR}/config.h
    )

# add the binary tree to the search path for include files
# so that we will find config.h
include_directories("${CMAKE_CURRENT_BINARY_DIR}")
