cmake_minimum_required(VERSION 3.14)
include(ExternalProject)
include(FetchContent)

project("Rigs of Rods" VERSION "2021.04")

option(RUN_PMM "Run PMM" OFF)
option(DOWNLOAD_CONTENTPACK "Download the ContentPacks" OFF)

if(RUN_PMM)
    include("${CMAKE_SOURCE_DIR}/pmm.cmake")
    file(DOWNLOAD "https://raw.githubusercontent.com/AnotherFoxGuy/rigs-of-rods/to-cloudsmith/conanfile.txt" "${CMAKE_SOURCE_DIR}/conanfile.txt")
    pmm(CONAN REMOTES rigs-of-rods-deps https://conan.cloudsmith.io/rigs-of-rods/deps/ BINCRAFTERS)
endif()

set(BLT_OUTPUT_DIR "${CMAKE_BINARY_DIR}/redist")
file(MAKE_DIRECTORY ${BLT_OUTPUT_DIR})
set_property(DIRECTORY PROPERTY EP_BASE ${CMAKE_BINARY_DIR})

file(READ hash.txt ROR_GIT_TAG)

ExternalProject_Add(
    rigs-of-rods
    GIT_REPOSITORY https://github.com/AnotherFoxGuy/rigs-of-rods.git
    GIT_TAG ${ROR_GIT_TAG}
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
    GIT_REPOSITORY https://github.com/AnotherFoxGuy/ogremeshy.git
    GIT_TAG 8b815f62b00218672f56fa1449fa26d14cefaa15
    GIT_SHALLOW TRUE
    GIT_PROGRESS TRUE
    CMAKE_ARGS
    -DCMAKE_INSTALL_PREFIX=${BLT_OUTPUT_DIR}
    -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
    -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
    -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}
    -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
)

ExternalProject_Add(
    ror-server
    GIT_REPOSITORY https://github.com/RigsOfRods/ror-server.git
    GIT_TAG 22d96fb4e304ed10981302049b0e324505488169
    GIT_SHALLOW TRUE
    GIT_PROGRESS TRUE
    CMAKE_ARGS
    -DRORSERVER_WITH_ANGELSCRIPT=ON
    -DCMAKE_INSTALL_PREFIX=${BLT_OUTPUT_DIR}
    -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
    -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}
    -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}
    -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}
)

macro(dl_contentpack url name hash)
    FetchContent_Declare(
        ${name}
        URL ${url}
        URL_MD5 ${hash}
        SOURCE_DIR "${BLT_OUTPUT_DIR}/content/${name}"
    )
    FetchContent_MakeAvailable(${name})
endmacro()

if(DOWNLOAD_CONTENTPACK)
    dl_contentpack("http://forum.rigsofrods.org/resources/content-pack-trucks-heavy-equipment.490/download" "HeavyEquipment"    "3e499cb0b4e93172d3198554d7478bef")
    dl_contentpack("http://forum.rigsofrods.org/resources/content-pack-cars-light-vehicles.491/download"    "LightVehicles"     "d3b421fc2c2446e90c7efed126e0413d")
    dl_contentpack("http://forum.rigsofrods.org/resources/content-pack-trailers-loads.492/download"         "Loads"             "c86e4d5c605f6c97efc4da25257061bb")
    dl_contentpack("http://forum.rigsofrods.org/resources/content-pack-air-sea.495/download"                "AirSea"            "a080d717a905a88b41e144833c05209a")
    dl_contentpack("http://forum.rigsofrods.org/resources/content-pack-trains.494/download"                 "Trains"            "0927db107a7d8a164f57c9b45d57f773")
    dl_contentpack("http://forum.rigsofrods.org/resources/content-pack-terrains.496/download"               "Terrains"          "a5f6c5142011d4f9bbfc6202ce2199cf")
endif()


INSTALL(
    DIRECTORY ${BLT_OUTPUT_DIR}/
    DESTINATION .
    USE_SOURCE_PERMISSIONS
    PATTERN "*.lib" EXCLUDE
)


 # CPack
 set(CPACK_PACKAGE_DESCRIPTION "Rigs of Rods soft-body physics simulator")
 set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Rigs of Rods is an open source vehicle simulator based on soft-body physics.")
 set(CPACK_PACKAGE_CONTACT "Edgar@AnotherFoxGuy.com")
 set(CPACK_PACKAGE_VENDOR "AnotherFoxGuy")
 SET(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
 SET(CPACK_PACKAGE_VERSION_MAJOR ${PROJECT_VERSION_MAJOR})
 SET(CPACK_PACKAGE_VERSION_MINOR ${PROJECT_VERSION_MINOR})
 SET(CPACK_PACKAGE_VERSION_PATCH ${PROJECT_VERSION_PATCH})
 set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_SOURCE_DIR}/resources/license.rtf")
 SET(CPACK_PACKAGE_EXECUTABLES "RoR;Rigs of Rods Simulator")
 set(CPACK_NSIS_EXECUTABLES_DIRECTORY ".")
 set(CPACK_NSIS_MODIFY_PATH "ON")
 set(CPACK_NSIS_HELP_LINK "https://discord.gg/rigsofrods")
 set(CPACK_NSIS_URL_INFO_ABOUT "https://rigsofrods.org")
 SET(CPACK_NSIS_INSTALLED_ICON_NAME "RoR.exe")
 SET(CPACK_NSIS_MUI_ICON "${CMAKE_SOURCE_DIR}/resources/ror.ico")
 SET(CPACK_NSIS_MUI_UNIICON "${CMAKE_SOURCE_DIR}/resources/ror.ico")
 # SET(CPACK_NSIS_MUI_HEADERIMAGE "${CMAKE_SOURCE_DIR}/resources/RoRSetupLarge.bmp")

 
 IF (MSVC)
     set(CPACK_PACKAGE_NAME "Rigs of Rods")
     SET(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${PROJECT_VERSION}")
     SET(CPACK_GENERATOR ZIP)# ZIP;
 ENDIF (MSVC)

 include(CPack)