# Vulkan SDK C++ Starter Project

### Dependiencies

[CMake 3.13.4](https://cmake.org/files/v3.13/cmake-3.13.4-Linux-x86_64.tar.gz) version is minimum required.

#### Linux command (Ubuntu)
```
  sudo apt-get install libglm-dev cmake libxcb-dri3-0 libxcb-present0 libpciaccess0 \
    libpng-dev libxcb-keysyms1-dev libxcb-dri3-dev libx11-dev g++ gcc g++-multilib \
    libmirclient-dev libwayland-dev libxrandr-dev libxcb-ewmh-dev git python3 bison libxcb1-dev
```

### Requirements

It works only at Linux currently, but there is plan to make it working on Windows as well.

### Build

 - mkdir build
 - cd build
 - cmake ..
 - make

### TODO
 - add checking if third party libraries have been already installed (to reduce build size)
 - add Windows version
