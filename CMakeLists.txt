# https://gitlab.com/CLIUtils/modern-cmake/-/blob/master/examples/extended-project/CMakeLists.txt

cmake_minimum_required(VERSION 3.19)

# Project name and a few useful settings. Other commands can pick up the results
project(template
        VERSION 0.1
        DESCRIPTION ""
        LANGUAGES C)

set(HEADER_LIST
        "${template_SOURCE_DIR}/include/common.h"
        )

set(COMMON_SOURCE_LIST
        "${template_SOURCE_DIR}/src/common.c"
        )

set(PROG1_SOURCE_LIST
        )

set(PROG2_SOURCE_LIST
        )

set(PROG1_MAIN_SOURCE
        "${template_SOURCE_DIR}/src/prog1.c"
        )

set(PROG2_MAIN_SOURCE
        "${template_SOURCE_DIR}/src/prog2.c"
        )

### Require out-of-source builds
# this still creates a CMakeFiles directory and CMakeCache.txt- can we delete them?
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)
if (EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "You cannot build in a source directory (or any directory with a CMakeLists.txt file). Please make a build subdirectory. Feel free to remove CMakeCache.txt and CMakeFiles.")
endif ()

# Only do these if this is the main project, and not if it is included through add_subdirectory
if (CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)

    # Optionally set things like CMAKE_CXX_STANDARD, CMAKE_POSITION_INDEPENDENT_CODE here
    set(CMAKE_C_STANDARD 11)
    set(CMAKE_C_EXTENSIONS OFF)

    # Let's nicely support folders in IDEs
    set_property(GLOBAL PROPERTY USE_FOLDERS ON)

    # Testing only available if this is the main app
    # Note this needs to be done in the main CMakeLists
    # since it calls enable_testing, which must be in the
    # main CMakeLists.
    include(CTest)

    # Docs only available if this is the main app
    find_package(Doxygen)

    if (Doxygen_FOUND)
        add_subdirectory(docs)
    else ()
        message(STATUS "Doxygen not found, not building docs")
    endif ()
endif ()

# The compiled library code is here
add_subdirectory(src)

find_library(LIBCGREEN cgreen)

# Testing only available if this is the main app
if ((CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME) AND BUILD_TESTING AND LIBCGREEN)
    add_subdirectory(tests)
endif ()
