# CMakeLists.txt - glm
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

if (UNIX)
  message ("Checking and eventually installing newest GLM (OpenGL Mathematics)")

  include(ExternalProject)

  ExternalProject_Add(glm
    #if you want use newest glm please remove -DGIT_TAG flag
    #DOWNLOAD_COMMAND ${CMAKE_COMMAND} -DGIT_REPO=https://github.com/g-truc/glm -DGIT_TAG=27f8e5b0a161b49f06ff259ca783dabd7a9498fd -DGIT_REPO_DIR=glm -P ${PROJECT_SOURCE_DIR}/cmake/git_sparse_checkout.cmake
    DOWNLOAD_COMMAND ${CMAKE_COMMAND} -DGIT_REPO=https://github.com/g-truc/glm -DGIT_REPO_DIR=glm -P ${PROJECT_SOURCE_DIR}/cmake/git_sparse_checkout.cmake
    PREFIX "${CMAKE_CURRENT_BINARY_DIR}"
    # Disable configure, build and install step
    CONFIGURE_COMMAND ""
    BUILD_COMMAND
      cmake -DBUILD_SHARED_LIBS=true ../glm -B . &&
      make
    INSTALL_COMMAND
      echo "Installing glm into system folders (/usr/local). Your password may be needed." &&
      sudo mv libglm_shared.so /usr/local/lib/ &&
      sudo cp -r ../glm /usr/local/include/
    )

  # Specify include dir
  ExternalProject_Get_Property(glm source_dir)
  set(GLM_INCLUDE_DIRS /usr/local/include/ PARENT_SCOPE)
  set(GLM_LIBRARIES /usr/local/lib/libglm_shared.so PARENT_SCOPE)

else ()
  message(FATAL_ERROR "No support for operational system different than Unix for now.")
endif()
