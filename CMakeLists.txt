cmake_minimum_required(VERSION 3.12)
project(PiecewiseGeometricModelIndex
        VERSION 1.0
        HOMEPAGE_URL https://github.com/gvinciguerra/PGM-index)

option(BUILD_PGM_TUNER "Build the tuner target" OFF)

set(CMAKE_CXX_STANDARD 17)

include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-march=native" COMPILER_SUPPORTS_MARCH_NATIVE)
if (COMPILER_SUPPORTS_MARCH_NATIVE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native")
endif ()

CHECK_CXX_COMPILER_FLAG("-ftree-vectorize" COMPILER_SUPPORTS_FTREE_VECTORIZE)
if (COMPILER_SUPPORTS_FTREE_VECTORIZE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -ftree-vectorize")
endif ()

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
endif ()

find_package(OpenMP)
if (OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif ()

include_directories(include)

# PGM-index library
add_library(pgmindexlib INTERFACE)
file ( GLOB ALL_HEADERS "include/pgm/*.hpp" )
target_sources(pgmindexlib INTERFACE ${ALL_HEADERS})
target_include_directories(pgmindexlib INTERFACE include/)

# tuner
if (${BUILD_PGM_TUNER})
    add_subdirectory(tuner)
endif ()

if (${PGM_TEST})
    enable_testing()
    add_subdirectory(test)
endif()
