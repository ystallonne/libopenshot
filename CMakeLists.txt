####################### CMakeLists.txt (libopenshot) #########################
# @brief CMake build file for libopenshot (used to generate makefiles)
# @author Jonathan Thomas <jonathan@openshot.org>
#
# @section LICENSE
#
# Copyright (c) 2008-2019 OpenShot Studios, LLC
# <http://www.openshotstudios.com/>. This file is part of
# OpenShot Library (libopenshot), an open-source project dedicated to
# delivering high quality video editing and animation solutions to the
# world. For more information visit <http://www.openshot.org/>.
#
# OpenShot Library (libopenshot) is free software: you can redistribute it
# and/or modify it under the terms of the GNU Lesser General Public License
# as published by the Free Software Foundation, either version 3 of the
# License, or (at your option) any later version.
#
# OpenShot Library (libopenshot) is distributed in the hope that it will be
# useful, but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
# GNU Lesser General Public License for more details.
#
# You should have received a copy of the GNU Lesser General Public License
# along with OpenShot Library. If not, see <http://www.gnu.org/licenses/>.
################################################################################

cmake_minimum_required(VERSION 3.1...3.14 FATAL_ERROR)

message("\
-----------------------------------------------------------------
          Welcome to the OpenShot Build System!

CMake will now check libopenshot's build dependencies and inform
you of any missing files or other issues.

For more information, please visit <http://www.openshot.org/>.
-----------------------------------------------------------------")

################ ADD CMAKE MODULES ##################
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules")

################ GET VERSION INFORMATION FROM VERSION.H ##################
message(STATUS "Determining Version Number (from Version.h file)")

#### Get the lines related to libopenshot version from the Version.h header
file(STRINGS ${CMAKE_CURRENT_SOURCE_DIR}/include/Version.h
     OPENSHOT_VERSION_LINES
     REGEX "#define[ ]+OPENSHOT_VERSION_.*[0-9]+;.*")

#### Set each line into it's own variable
list (GET OPENSHOT_VERSION_LINES 0 LINE_MAJOR)
list (GET OPENSHOT_VERSION_LINES 1 LINE_MINOR)
list (GET OPENSHOT_VERSION_LINES 2 LINE_BUILD)
list (GET OPENSHOT_VERSION_LINES 3 LINE_SO)

#### Get the version number out of each line
STRING(REGEX REPLACE "#define[ ]+OPENSHOT_VERSION_MAJOR[ ]+([0-9]+);(.*)" "\\1" MAJOR_VERSION "${LINE_MAJOR}")
STRING(REGEX REPLACE "#define[ ]+OPENSHOT_VERSION_MINOR[ ]+([0-9]+);(.*)" "\\1" MINOR_VERSION "${LINE_MINOR}")
STRING(REGEX REPLACE "#define[ ]+OPENSHOT_VERSION_BUILD[ ]+([0-9]+);(.*)" "\\1" BUILD_VERSION "${LINE_BUILD}")
STRING(REGEX REPLACE "#define[ ]+OPENSHOT_VERSION_SO[ ]+([0-9]+);(.*)" "\\1" SO_VERSION "${LINE_SO}")

message(STATUS "MAJOR Version: ${MAJOR_VERSION}")
message(STATUS "MINOR Version: ${MINOR_VERSION}")
message(STATUS "BUILD Version: ${BUILD_VERSION}")
message(STATUS "SO/API/ABI Version: ${SO_VERSION}")
message(STATUS "Determining Version Number - done")

################### SETUP PROJECT ###################
PROJECT(libopenshot LANGUAGES C CXX
        VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${BUILD_VERSION})

message("
Generating build files for OpenShot
  Building ${PROJECT_NAME} (version ${PROJECT_VERSION})
  SO/API/ABI Version: ${SO_VERSION}
")

#### Enable C++11 (for std::shared_ptr support)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

IF (WIN32)
	SET_PROPERTY(GLOBAL PROPERTY WIN32 "WIN32")
ENDIF(WIN32)

############## FIND ALL QT RELATED HEADERS ##############
set(QT_HEADER_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include/Qt)
FILE(GLOB QT_HEADER_FILES "${QT_HEADER_DIR}/*.h")

############## PROCESS SUB-DIRECTORIES ##############
add_subdirectory(src)
add_subdirectory(tests)

################### DOCUMENTATION ###################
# Find Doxygen (used for documentation)
include(cmake/Modules/UseDoxygen.cmake)

file(GLOB_RECURSE doc_files ${CMAKE_CURRENT_BINARY_DIR}/doc/html/*.*)
INSTALL(FILES ${doc_files} DESTINATION share/doc/libopenshot)
