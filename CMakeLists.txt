cmake_minimum_required(VERSION 2.6)
project(pr-downloader)

IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Release CACHE STRING
      "Choose the type of build, options are: None DEBUG RELEASE."
      FORCE)
ENDIF(NOT CMAKE_BUILD_TYPE)

set (CMAKE_CXX_FLAGS_DEBUG "-DDEBUG")
set (CMAKE_CXX_FLAGS_RELEASE "")


if   (MINGW)
        set(MINGWLIBS "${CMAKE_SOURCE_DIR}/mingwlibs" CACHE PATH "Location of the mingwlibs package")
        if(NOT EXISTS "${MINGWLIBS}" OR NOT IS_DIRECTORY "${MINGWLIBS}")
                 MESSAGE(FATAL_ERROR "MINGWLIBS is not a valid directory")
        endif ()
        include_directories(${MINGWLIBS}/include)
        message(${MINGWLIBS})
#FIXME: remove hardcoded path
        include_directories("/usr/i586-mingw32msvc/include/")
        set(CMAKE_LIBRARY_PATH ${MINGWLIBS}/lib)
        set(CMAKE_PREFIX_PATH ${MINGWLIBS})
        set(CMAKE_FIND_ROOT_PATH ${MINGWLIBS} ${CMAKE_FIND_ROOT_PATH})
        set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
        set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
        set(CMAKE_SHARED_LIBRARY_PREFIX "")
        set(CMAKE_SHARED_MODULE_PREFIX "")

        set(CMAKE_CXX_FLAGS "-Wall -O2 -g -D_WIN32_WINNT=0x0501 -DWIN32 -D_WINDOWS")
else ()
	set(CMAKE_CXX_FLAGS "-Wall -O0 -g -fPIC")
	set(CMAKE_SKIP_BUILD_RPATH  FALSE)
	set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
	set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
endif ()
set(CMAKE_C_FLAGS ${CMAKE_CXX_FLAGS})

find_package(CURL REQUIRED)
find_package(ZLIB REQUIRED)
include(FindPkgConfig)

include_directories(${CURL_INCLUDE_DIR})
include_directories(${ZLIB_INCLUDE_DIR})
include_directories("src")
include_directories(".")

#----------------------------------------------------------------------------------------------------
# libtorrent-rasterbar dependency check
#----------------------------------------------------------------------------------------------------

IF (WIN32)
        SET(ADDITIONAL_LIBS -mthreads ws2_32 mswsock)
ENDIF ()

subdirs(src)

