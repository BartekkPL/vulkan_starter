# CMakeLists.txt - vulkanSDK
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

set(VULKAN_DEFAULT_VERSION 1.2.131.2)
set(VULKAN_DEFAULT_VERSION_HASH 8ac309392785b798e5d526795f9258e2c1e2858ee40e866bcb292a54c891f082)

if (UNIX)
  message ("Checking and eventually installing Vulkan SDK")

  include(ExternalProject)

  set(PROJECT_NAME vulkanSDK)
  set(VULKAN_SDK_SOURCE_DIR ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-prefix/src/${PROJECT_NAME}/x86_64)

  # allows for set VULKAN_INSTALLATION_PREFIX when configure cmake command
  if (NOT DEFINED VULKAN_INSTALLATION_PREFIX)
    set(VULKAN_INSTALLATION_PREFIX /usr/local)
  endif()

  if (NOT DEFINED VULKAN_REQUIRED_VERSION)
    set(VULKAN_REQUIRED_VERSION ${VULKAN_DEFAULT_VERSION})
  endif()

  string(REPLACE "." ";" VERSION_LIST ${VULKAN_REQUIRED_VERSION})
  list(GET VERSION_LIST 0 VULKAN_VERSION_MAJOR)
  list(GET VERSION_LIST 1 VULKAN_VERSION_MINOR)
  list(GET VERSION_LIST 2 VULKAN_VERSION_PATCH)
  list(GET VERSION_LIST 3 VULKAN_VERSION_REVISION)

  if (NOT DEFINED VULKAN_REQUIRED_VERSION_HASH)
    set(VULKAN_REQUIRED_VERSION_HASH ${VULKAN_DEFAULT_VERSION_HASH})
  endif()

  ExternalProject_Add(${PROJECT_NAME}
    DOWNLOAD_COMMAND
      URL https://sdk.lunarg.com/sdk/download/${VULKAN_REQUIRED_VERSION}/linux/vulkansdk-linux-x86_64-${VULKAN_REQUIRED_VERSION}.tar.gz
      URL_HASH SHA256=${VULKAN_REQUIRED_VERSION_HASH}
    # Disable install step
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
    INSTALL_COMMAND
      echo "Installing Vulkan SDK into system folders (${VULKAN_INSTALLATION_PREFIX}). Your password may be needed." &&
      sudo cp -r ${VULKAN_SDK_SOURCE_DIR}/include/vulkan                                      ${VULKAN_INSTALLATION_PREFIX}/include/ &&
      sudo cp -r ${VULKAN_SDK_SOURCE_DIR}/lib/libvulkan.so                                    ${VULKAN_INSTALLATION_PREFIX}/lib/ &&
      sudo cp -r ${VULKAN_SDK_SOURCE_DIR}/lib/libvulkan.so.${VULKAN_VERSION_MAJOR}            ${VULKAN_INSTALLATION_PREFIX}/lib/ &&
      sudo cp -r ${VULKAN_SDK_SOURCE_DIR}/lib/libvulkan.so.${VULKAN_VERSION_MAJOR}.${VULKAN_VERSION_MINOR}.${VULKAN_VERSION_PATCH}       ${VULKAN_INSTALLATION_PREFIX}/lib/ &&
      sudo cp -r ${VULKAN_SDK_SOURCE_DIR}/lib/libVkLayer_khronos_validation.so                                    ${VULKAN_INSTALLATION_PREFIX}/lib/ &&
      sudo mkdir -p ${VULKAN_INSTALLATION_PREFIX}/share/vulkan/explicit_layer.d &&
      sudo cp -r ${VULKAN_SDK_SOURCE_DIR}/etc/vulkan/explicit_layer.d/.                       ${VULKAN_INSTALLATION_PREFIX}/share/vulkan/explicit_layer.d/
  )

  # sets needed variables
  set(VULKAN_SDK ${VULKAN_INSTALLATION_PREFIX} PARENT_SCOPE)
  set(VK_LAYER_PATH ${VULKAN_INSTALLATION_PREFIX}/share/vulkan/explicit_layer.d PARENT_SCOPE)
  set(VULKAN_SDK_INCLUDE_DIRS ${VULKAN_INSTALLATION_PREFIX}/include PARENT_SCOPE)
  set(VULKAN_SDK_LIBRARIES
    ${VULKAN_INSTALLATION_PREFIX}/lib/libvulkan.so
    ${VULKAN_INSTALLATION_PREFIX}/lib/libVkLayer_khronos_validation.so
    PARENT_SCOPE
    )

else ()
  message(FATAL_ERROR "No support for operational system different than Unix for now.")
endif()
