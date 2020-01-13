cmake_minimum_required(VERSION 3.14)
include(ExternalProject)

project(ror-release)


set(BLT_OUTPUT_DIR " ${CMAKE_BINARY_DIR}/redist")


ExternalProject_Add(
    rigs-of-rods
    GIT_REPOSITORY https://github.com/RigsOfRods/rigs-of-rods.git
    GIT_SHALLOW TRUE
    GIT_PROGRESS TRUE
    CMAKE_ARGS
    -DBUILD_DEV_VERSION=OFF
    -DCMAKE_INSTALL_PREFIX=${BLT_OUTPUT_DIR}
    -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
    -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
    -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}
    -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
)
ExternalProject_Add(
    ogremeshy
    GIT_REPOSITORYhttps://github.com/AnotherFoxGuy/ogremeshy.git
    GIT_SHALLOW TRUE
    GIT_PROGRESS TRUE
    CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${BLT_OUTPUT_DIR}
    -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
    -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
    -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}
    -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
)
    
    
