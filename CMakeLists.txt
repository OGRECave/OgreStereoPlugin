#-------------------------------------------------------------------
# This file is part of the CMake build system for OGRE
#     (Object-oriented Graphics Rendering Engine)
# For the latest info, see http://www.ogre3d.org/
#
# The contents of this file are placed in the public domain. Feel
# free to make use of it in any way you like.
#-------------------------------------------------------------------

cmake_minimum_required(VERSION 2.8)

# Find Ogre
set(CMAKE_PREFIX_PATH ${OGRE_INSTALL_PATH})
set(CMAKE_FIND_ROOT_PATH ${OGRE_INSTALL_PATH} "${CMAKE_FIND_ROOT_PATH}")
set(CMAKE_MODULE_PATH  ${OGRE_INSTALL_PATH}/lib/OGRE/cmake/)
find_package(OGRE QUIET REQUIRED)

# Configure StereoManager build
file(GLOB HEADER_FILES "${CMAKE_CURRENT_SOURCE_DIR}/include/*.h")
file(GLOB SOURCE_FILES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include ${OGRE_INCLUDE_DIRS})
add_definitions(-D_USRDLL)

add_library(Plugin_StereoManager ${OGRE_LIB_TYPE} ${HEADER_FILES} ${SOURCE_FILES})

target_link_libraries(Plugin_StereoManager ${OGRE_LIBRARIES} ${Boost_LIBRARIES})
if (NOT OGRE_STATIC)
  set_target_properties(Plugin_StereoManager PROPERTIES
    COMPILE_DEFINITIONS OGRE_STEREOPLUGIN_EXPORTS
  ) 
endif ()


# Modify Plugin Name
if (CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  # disable "lib" prefix on Unix
  set_target_properties(Plugin_StereoManager PROPERTIES PREFIX "")
  
  if(NOT OGRE_STATIC)
    set_target_properties(Plugin_StereoManager PROPERTIES SUFFIX ".so.${OGRE_VERSION}")
  endif()
endif()


# Add additional install files
file(GLOB RESOURCE_FILES 
	${CMAKE_CURRENT_SOURCE_DIR}/resources/Stereoscopy.cg
	${CMAKE_CURRENT_SOURCE_DIR}/resources/Stereoscopy.compositor
	${CMAKE_CURRENT_SOURCE_DIR}/resources/Stereoscopy.material)


# Install files
install(FILES ${RESOURCE_FILES} DESTINATION ${OGRE_INSTALL_PATH}/share/OGRE/Media/materials/scripts)
install(FILES ${HEADER_FILES} DESTINATION ${OGRE_INSTALL_PATH}/include/OGRE/Plugins/StereoManager)
install(TARGETS Plugin_StereoManager DESTINATION ${OGRE_INSTALL_PATH}/lib/OGRE/)
