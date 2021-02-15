cmake_minimum_required(VERSION 3.4.3)

project("SVF")

# To support both in- and out-of-source builds,
# we check for the presence of the add_llvm_loadable_module command.
# - if this command is not present, we are building out-of-source
if(NOT COMMAND add_llvm_library)
    set(CMAKE_CXX_STANDARD 14)
    if(CMAKE_BUILD_TYPE MATCHES "Debug")
        set(CMAKE_CXX_FLAGS "-fPIC -std=gnu++14 -g -O0 -fno-rtti -Wno-deprecated -DHAVE_IEEE_754 -DSIZEOF_VOID_P=8 -DSIZEOF_LONG=8")
    else()
        set(CMAKE_CXX_FLAGS "-fPIC -std=gnu++14 -O3 -fno-rtti -Wno-deprecated -DHAVE_IEEE_754 -DSIZEOF_VOID_P=8 -DSIZEOF_LONG=8")
    endif()
    set(CMAKE_C_FLAGS "-fPIC")
    find_package(LLVM REQUIRED CONFIG)
        list(APPEND CMAKE_MODULE_PATH "${LLVM_CMAKE_DIR}")
        include(AddLLVM)
        add_definitions(${LLVM_DEFINITIONS})
        include_directories(SYSTEM ${LLVM_INCLUDE_DIRS})
endif()
set(Z3_DIR $ENV{Z3_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include
                    ${CMAKE_CURRENT_BINARY_DIR}/include
                    ${Z3_DIR}/include/)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/Test-Suite)

LINK_DIRECTORIES(${Z3_DIR}/bin)
add_subdirectory(lib)
add_subdirectory(tools)

INSTALL(
    DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ ${CMAKE_CURRENT_BINARY_DIR}/include/ ${Z3_DIR}/include/
    COMPONENT devel
    DESTINATION include/svf
    FILES_MATCHING
    PATTERN "**/*.h"
    )
enable_testing()
add_subdirectory(Test-Suite)
include(CTest)
