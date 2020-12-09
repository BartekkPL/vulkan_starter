# CMakeLists.txt - glfw
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

set(GLFW_DEFAULT_VERSION 3.3.2)
set(GLFW_DEFAULT_VERSION_HASH 08a33a512f29d7dbf78eab39bd7858576adcc95228c9efe8e4bc5f0f3261efc7)

if (UNIX)
  message ("Checking and eventually installing GLFW library")

  include(ExternalProject)

  set(PROJECT_NAME glfw)
  set(GLFW_SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-prefix/src/${PROJECT_NAME})

  # allows for set GLFW_INSTALLATION_PREFIX when configure cmake command
  if (NOT DEFINED GLFW_INSTALLATION_PREFIX)
    set(GLFW_INSTALLATION_PREFIX /usr/local)
  endif()

  if (NOT DEFINED GLFW_REQUIRED_VERSION)
    set(GLFW_REQUIRED_VERSION ${GLFW_DEFAULT_VERSION})
  endif()

  string(REPLACE "." ";" VERSION_LIST ${GLFW_REQUIRED_VERSION})
  list(GET VERSION_LIST 0 GLFW_VERSION_MAJOR)
  list(GET VERSION_LIST 1 GLFW_VERSION_MINOR)
  list(GET VERSION_LIST 2 GLFW_VERSION_PATCH)

  if (NOT DEFINED GLFW_REQUIRED_VERSION_HASH)
    set(GLFW_REQUIRED_VERSION_HASH ${GLFW_DEFAULT_VERSION_HASH})
  endif()

  ExternalProject_Add(${PROJECT_NAME}
    DOWNLOAD_COMMAND
      URL https://github.com/glfw/glfw/releases/download/${GLFW_REQUIRED_VERSION}/glfw-${GLFW_REQUIRED_VERSION}.zip
      URL_HASH SHA256=${GLFW_REQUIRED_VERSION_HASH}
    # Disable install step
    CONFIGURE_COMMAND ""
    BUILD_COMMAND
      cmake -DCMAKE_INSTALLATION_PREFIX=${GLFW_INSTALLATION_PREFIX} -DBUILD_SHARED_LIBS=true ../glfw -B . &&
      make
    INSTALL_COMMAND ""
      echo "Installing GLFW into system folders (${GLFW_INSTALLATION_PREFIX}). Your password may be needed." &&
      sudo make install
  )

  # sets needed variables
  set(GLFW_INCLUDE_DIRS ${GLFW_INSTALLATION_PREFIX}/include PARENT_SCOPE)
  set(GLFW_LIBRARIES
    ${GLFW_INSTALLATION_PREFIX}/lib/libglfw.so
    PARENT_SCOPE
    )

else ()
  message(FATAL_ERROR "No support for operational system different than Unix for now.")
endif()
