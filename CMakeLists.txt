cmake_minimum_required(VERSION 3.5)
project(blepp CXX)

set(LIBBLEPP_MAJOR_VERSION 0)
set(LIBBLEPP_MINOR_VERSION 5)
set(LIBBLEPP_PATCH_VERSION 0)
set(LIBBLEPP_VERSION ${LIBBLEPP_MAJOR_VERSION}.${LIBBLEPP_MINOR_VERSION}.${LIBBLEPP_PATCH_VERSION})
 
# Offer the user the choice of overriding the installation directories
set(INSTALL_LIB_DIR     lib     CACHE PATH "Installation directory for libraries")
set(INSTALL_BIN_DIR     bin     CACHE PATH "Installation directory for executables")
set(INSTALL_INCLUDE_DIR include CACHE PATH "Installation directory for header files")
set(DEF_INSTALL_CMAKE_DIR lib/CMake/LibBlepp)
set(INSTALL_CMAKE_DIR ${DEF_INSTALL_CMAKE_DIR} CACHE PATH "Installation directory for CMake files")
 
# Make relative paths absolute (needed later on)
foreach(p LIB BIN INCLUDE CMAKE)
  set(var INSTALL_${p}_DIR)
  if(NOT IS_ABSOLUTE "${${var}}")
    set(${var} "${CMAKE_INSTALL_PREFIX}/${${var}}")
  endif()
endforeach()
 
## To find Bluz module...
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake/modules)
## ...used here
find_package(Bluez REQUIRED)

# set up include-directories
include_directories(include "${PROJECT_SOURCE_DIR}" "${PROJECT_BINARY_DIR}" ${BLUEZ_INCLUDE_DIRS})
 
#Set CXX standard
ADD_DEFINITIONS(
      -std=c++11
)

# Add sub-directories
add_subdirectory(${PROJECT_NAME})
add_subdirectory(examples)


# Install LibBleppConfig*.cmake files
# ===================================
# Add all targets to the build-tree export set
export(TARGETS ${PROJECT_NAME} FILE "${PROJECT_BINARY_DIR}/LibBleppTargets.cmake")
 
# Export the package for use from the build-tree
# (this registers the build-tree with a global CMake-registry)
export(PACKAGE LibBlepp)
 
# Create the LibBleppConfig.cmake and LibBleppConfigVersion files
file(RELATIVE_PATH REL_INCLUDE_DIR "${INSTALL_CMAKE_DIR}" "${INSTALL_INCLUDE_DIR}")
# ... for the build tree
set(CONF_INCLUDE_DIRS include)
set(CONF_LIBNAME "${PROJECT_NAME}")
set(CONF_LIBRARY_DIRS lib)
configure_file(LibBleppConfig.cmake.in "${PROJECT_BINARY_DIR}/LibBleppConfig.cmake" @ONLY)
# ... for the install tree
set(CONF_INCLUDE_DIRS "${CMAKE_INSTALL_PREFIX}/${CONF_INCLUDE_DIRS}")
set(CONF_LIBRARY_DIRS "${CMAKE_INSTALL_PREFIX}/${CONF_LIBRARY_DIRS}")
configure_file(LibBleppConfig.cmake.in "${PROJECT_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/LibBleppConfig.cmake" @ONLY)
# ... for both
configure_file(LibBleppConfigVersion.cmake.in "${PROJECT_BINARY_DIR}/LibBleppConfigVersion.cmake" @ONLY)
 
# Install the LibBleppConfig.cmake and LibBleppConfigVersion.cmake
install(FILES
  "${PROJECT_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/LibBleppConfig.cmake"
  "${PROJECT_BINARY_DIR}/LibBleppConfigVersion.cmake"
  DESTINATION "${INSTALL_CMAKE_DIR}" COMPONENT dev)
 
# Install the export set for use with the install-tree
install(EXPORT LibBleppTargets DESTINATION "${INSTALL_CMAKE_DIR}" COMPONENT dev)

