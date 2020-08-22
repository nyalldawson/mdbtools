#############################################################
# CMake settings
CMAKE_MINIMUM_REQUIRED(VERSION 3.1.0)
SET(CMAKE_COLOR_MAKEFILE ON)

# set path to additional CMake modules
SET(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})


#############################################################
# Project and version
SET(CPACK_PACKAGE_VERSION_MAJOR "0")
SET(CPACK_PACKAGE_VERSION_MINOR "8")
SET(CPACK_PACKAGE_VERSION_PATCH "2")
SET(COMPLETE_VERSION ${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH})
SET(RELEASE_NAME "Master")
PROJECT(mdbtools LANGUAGES C VERSION ${COMPLETE_VERSION})

SET (DEFAULT_BIN_SUBDIR     bin)
SET (DEFAULT_LIB_SUBDIR     lib${LIB_SUFFIX})
SET (DEFAULT_INCLUDE_SUBDIR include/mdbtools)

SET (MDBTOOLS_BIN_SUBDIR     ${DEFAULT_BIN_SUBDIR}     CACHE STRING "Subdirectory where executables will be installed")
SET (MDBTOOLS_LIB_SUBDIR     ${DEFAULT_LIB_SUBDIR}     CACHE STRING "Subdirectory where libraries will be installed")
SET (MDBTOOLS_INCLUDE_SUBDIR ${DEFAULT_INCLUDE_SUBDIR} CACHE STRING "Subdirectory where header files will be installed")

# mark *_SUBDIR variables as advanced as this is not something
# that an average user would use
MARK_AS_ADVANCED (MDBTOOLS_BIN_SUBDIR MDBTOOLS_LIB_SUBDIR MDBTOOLS_INCLUDE_SUBDIR)

# full paths for the installation
SET (MDBTOOLS_BIN_DIR     ${MDBTOOLS_BIN_SUBDIR})
SET (MDBTOOLS_LIB_DIR     ${MDBTOOLS_LIB_SUBDIR})
SET (MDBTOOLS_INCLUDE_DIR ${MDBTOOLS_INCLUDE_SUBDIR})

# set the default locations where the targets (executables, libraries) will land when compiled
# this is to allow running mdbtools utils from the source tree without having to actually do a "make install"
SET (MDBTOOLS_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/output)
SET (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${MDBTOOLS_OUTPUT_DIRECTORY}/${MDBTOOLS_BIN_SUBDIR})
SET (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${MDBTOOLS_OUTPUT_DIRECTORY}/${MDBTOOLS_LIB_SUBDIR})

SET(WITH_EXTRAS TRUE CACHE BOOL "Include extra utilities (for debugging only)")

FIND_PACKAGE(BISON)
FIND_PACKAGE(FLEX)

IF (BISON_FOUND AND FLEX_FOUND)
    MESSAGE(STATUS "Found Flex/Bison - SQL enabled")
    ADD_DEFINITIONS(-DSQL)
    SET(WITH_SQL TRUE)
ELSE (BISON_FOUND AND FLEX_FOUND)
    MESSAGE(STATUS "Couldn't find Flex/Bison - SQL disabled")
    SET(WITH_SQL FALSE)
ENDIF (BISON_FOUND AND FLEX_FOUND)

FIND_PACKAGE(PkgConfig REQUIRED)
PKG_SEARCH_MODULE(GLIB glib-2.0)
IF (GLIB_FOUND)
  MESSAGE(STATUS "Found glib-2.0 - internal glib disabled")
  SET(FAKE_GLIB FALSE)
ELSE (GLIB_FOUND)
  MESSAGE(STATUS "Couldn't find glib-2.0 - using internal glib")
  SET(FAKE_GLIB TRUE)
ENDIF (GLIB_FOUND)

FIND_PACKAGE(ODBC)
IF (ODBC_FOUND)
    MESSAGE(STATUS "Found ODBC ${ODBC_LIBRARIES}")
    IF(WITH_SQL)
      SET(HAVE_ODBC TRUE)
      #TODO - handle iodbc, windows ODBC
      ADD_DEFINITIONS(-DUNIXODBC)
    ELSE(WITH_SQL)
      MESSAGE(STATUS "Cannot build ODBC when SQL support is disabled")
      SET(HAVE_ODBC TRUE)
    ENDIF(WITH_SQL)
ELSE (ODBC_FOUND)
    MESSAGE(STATUS "Couldn't ODBC library - ODBC disabled")
    SET(HAVE_ODBC FALSE)
ENDIF (ODBC_FOUND)




ADD_SUBDIRECTORY(src)
