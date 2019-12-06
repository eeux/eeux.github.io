---
layout: post
title: "cmake usage"
date: 2019-12-01 09:40
categories: tool
keywords: cmake
---





Cmake is a platform independent compile system, which is awesome for C++ developers.
This passage will introduce common project structure and cmake template...





#### project structure
A good project structure is very important for development. Others in your team can easily know what these files used for.

```text
|--CMakeLists.txt
|
|--build
|
|--include
|   |--test.h
|
|--src
|   |--main.cc
|   |--test.cc
|
|--doc
|
|--README.md
```

#### usage
We should go to directory `build` to run cmake command.
Otherwise, the entire project will be **polluted** by the output of cmake files. You just need to do like this:
```sh
$ cd build
$ cmake ..
$ make
```

##### Let's compare the difference of `cmake .` and `cmake ..`:
![bad-cmake-flow](https://github.com/eeux/blog-pictures/blob/master/2019/12/bad-cmake-flow.png?raw=true)
![good-cmake-flow](https://github.com/eeux/blog-pictures/blob/master/2019/12/good-cmake-build-flow.png?raw=true)

#### [file]: CmakeLists.txt
```cmake
# Set the minimum version of CMake that can be used
# To find the cmake version run
# $ cmake --version
cmake_minimum_required(VERSION 3.5)

# Set the project name
project (SolveKPath)

set(CMAKE_CXX_STANDARD 11)

# for debug or release options
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")

# Create a sources variable with a link to all cpp files to compile
set(SOURCES
    src/main.cc
    src/graph.cc
    src/sta.cc
    )

# Add an executable with the above sources
add_executable(SolveKPath ${SOURCES})

# Platform options
if(UNIX AND NOT APPLE)
    set(LINUX TRUE)
endif()

if(LINUX)
    message(STATUS ">>> Linux")
    set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/Linux_64)
else()
    message(STATUS ">>> Windows")
    set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/Windows_64)
endif()

# make debug
add_custom_target(debug
    COMMAND ${CMAKE_COMMAND} -DCMAKE_BUILD_TYPE=Debug ${CMAKE_SOURCE_DIR}
    COMMAND ${CMAKE_COMMAND} --build ${CMAKE_BINARY_DIR} --target all
    COMMENT "Switch CMAKE_BUILD_TYPE to Debug"
    )

# make release
add_custom_target(release
    COMMAND ${CMAKE_COMMAND} -DCMAKE_BUILD_TYPE=Release ${CMAKE_SOURCE_DIR}
    COMMAND ${CMAKE_COMMAND} --build ${CMAKE_BINARY_DIR} --target all
    COMMENT "Switch CMAKE_BUILD_TYPE to Release"
    )

# Set the directories that should be included in the build command for this target
# when running g++ these will be included as -I/directory/path/
target_include_directories(SolveKPath
    PRIVATE 
        ${PROJECT_SOURCE_DIR}/include
    )

# include the output directory, where the svnversion.h file is generated
include_directories(${CMAKE_CURRENT_BINARY_DIR})

# a custom target that is always built
add_custom_target(svnheader ALL
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/svnheader.h)

# creates svnheader.h using cmake script
add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/svnheader.h
    COMMAND ${CMAKE_COMMAND} -DSOURCE_DIR=${CMAKE_CURRENT_SOURCE_DIR}
                             -P ${CMAKE_SOURCE_DIR}/tools/share/cmake/getsvn.cmake)

# svnversion.h is a generated file
set_source_files_properties(${CMAKE_CURRENT_BINARY_DIR}/svnversion.h
    PROPERTIES GENERATED TRUE
    HEADER_FILE_ONLY TRUE)

# explicitly say that the executable depends on the svnheader
add_dependencies(SolveKPath svnheader)
```

#### [file]: get_svn.cmake
```cmake
# the FindSubversion.cmake module is part of the standard distribution
include(FindSubversion)
# extract working copy information for SOURCE_DIR into MY_XXX variables
Subversion_WC_INFO(${SOURCE_DIR} MY)
# write a file with the SVNVERSION define
file(WRITE svnversion.h.txt "#define SVNVERSION ${MY_WC_REVISION}\n")
# copy the file to the final header only if the version changes
# reduces needless rebuilds
execute_process(COMMAND ${CMAKE_COMMAND} -E copy_if_different
                        svnversion.h.txt svnversion.h)
```

#### summary
1. maintain a good project structure
2. create a directory specially used for build project

---
(done)
