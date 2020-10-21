PROJECT(yasm)
CMAKE_MINIMUM_REQUIRED(VERSION 2.4)
if (COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
endif (COMMAND cmake_policy)

OPTION(BUILD_SHARED_LIBS "Build shared libraries" OFF)

# Where to look first for cmake modules
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/modules")

INCLUDE(YasmMacros)
INCLUDE(VersionGen)

OPTION(ENABLE_NLS "Enable message translations" OFF)

OPTION(YASM_BUILD_TESTS "Enable building of tests" OFF)

IF(YASM_BUILD_TESTS)
    ENABLE_TESTING()
ENDIF(YASM_BUILD_TESTS)

# Default build type to debug if not set
IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Debug CACHE STRING
      "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
      FORCE)
ENDIF(NOT CMAKE_BUILD_TYPE)

VERSION_GEN(PACKAGE_VERSION "${CMAKE_BINARY_DIR}/YASM-VERSION-FILE" "1.3.0")

set (PACKAGE_STRING "yasm ${PACKAGE_VERSION}")

INCLUDE_DIRECTORIES(AFTER ${CMAKE_BINARY_DIR} ${yasm_SOURCE_DIR})

INCLUDE(ConfigureChecks.cmake)

if (MSVC)
	#set(CMAKE_CXX_FLAGS "$ENV{CXXFLAGS} ${CMAKE_CXX_FLAGS} /std:c++17 /std:c++latest -D_HAS_CXX17=1 -D_HAS_CXX20=1")
	#set(CMAKE_CXX_FLAGS "$ENV{CXXFLAGS} ${CMAKE_CXX_FLAGS} -DUWS_NO_ZLIB /std:c++17 /std:c++latest -D_HAS_CXX17=1 -D_HAS_CXX20=1")
    set(CompilerFlags
        CMAKE_CXX_FLAGS
        CMAKE_CXX_FLAGS_DEBUG
        CMAKE_CXX_FLAGS_RELEASE
        CMAKE_C_FLAGS
        CMAKE_C_FLAGS_DEBUG
        CMAKE_C_FLAGS_RELEASE
        )
    foreach(CompilerFlag ${CompilerFlags})
        string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
    endforeach()
	#list(APPEND uws_libraries
    #        libiconv
    #        libcurl
	#		zlibstatic)
else()
	#set(CMAKE_CXX_FLAGS "$ENV{CXXFLAGS} ${CMAKE_CXX_FLAGS} -std=c++17")
	#list(APPEND uws_libraries
	#		pthread
	#		stdc++fs
    #        libiconv
    #        libcurl
	#		zlibstatic)
    set(CMAKE_C_FLAGS "$ENV{CFLAGS} ${CMAKE_C_FLAGS} -fPIC")
    set(CMAKE_CXX_FLAGS "$ENV{CXXFLAGS} ${CMAKE_CXX_FLAGS} -fPIC")
endif(MSVC)

ADD_SUBDIRECTORY(tools)
ADD_SUBDIRECTORY(libyasm)
ADD_SUBDIRECTORY(modules)
ADD_SUBDIRECTORY(frontends)

INSTALL(FILES
    libyasm.h
    ${CMAKE_BINARY_DIR}/libyasm-stdint.h
    DESTINATION include
    )
