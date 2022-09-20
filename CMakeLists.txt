# CMakeList.txt : Top-level CMake project file, do global configuration
# and include sub-projects here.
#
cmake_policy(VERSION 3.15)
cmake_minimum_required (VERSION 3.15)

set(PRODUCT_NAME "My_Product_Name")
project(${PRODUCT_NAME} LANGUAGES C CXX VERSION 2.0.0)

set(LIBRARY_VERSION 400)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

#You must define one of the macros APL, IBM, or LIN to 1, depending on the OS you are compiling for.
if(APPLE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DAPL=1 -DIBM=0 -DLIN=0")
elseif(UNIX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DAPL=0 -DIBM=0 -DLIN=1")
elseif(WIN32)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DAPL=0 -DIBM=1 -DLIN=0 -D_CRT_SECURE_NO_WARNINGS")
endif()

if(WIN32)
    add_compile_definitions(NOMINMAX)
endif()

#Include sub-folders.
add_subdirectory ("src")

       
