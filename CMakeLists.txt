cmake_minimum_required(VERSION 2.8.3)
project(sam_webgui)

if (DEFINED ENV{EMSDK} OR ${ROSWASM_NATIVE})

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS roscpp roswasm_webgui sam_msgs smarc_msgs std_msgs sensor_msgs geometry_msgs nav_msgs)

###################################
## catkin specific configuration ##
###################################

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES sam_webgui
#  CATKIN_DEPENDS other_catkin_pkg
#  DEPENDS roswasm_webgui
)

###########
## Build ##
###########

if (${ROSWASM_NATIVE})
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")
#set(OpenGL_GL_PREFERENCE "GLVND")
find_package(GLEW REQUIRED)
find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)
find_package(SDL2 REQUIRED) 
find_package(SDL2_image REQUIRED)
endif()

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

if (${ROSWASM_NATIVE})
include_directories(
  ${OPENGL_INCLUDE_DIR}
  ${GLEW_INCLUDE_DIRS}
  ${SDL2_INCLUDE_DIR}
  ${SDL2_IMAGE_INCLUDE_DIRS}
)
endif()

#add_library(sam_webgui STATIC src/roswasm_sam.cpp)
add_library(sam_webgui src/roswasm_sam.cpp)
add_executable(sam_webgui_test src/main.cpp)

if (NOT ${ROSWASM_NATIVE})
set_target_properties(sam_webgui_test PROPERTIES OUTPUT_NAME "webgui.js")
configure_file(www/webgui.html ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.html COPYONLY)
target_link_libraries(sam_webgui ${roswasm_webgui_LIBRARIES} ${roswasm_LIBRARIES})
target_link_libraries(sam_webgui_test sam_webgui ${roswasm_webgui_LIBRARIES} ${roswasm_LIBRARIES})
else()
target_link_libraries(sam_webgui ${catkin_LIBRARIES} ${SDL2_LIBRARY} ${SDL2_IMAGE_LIBRARIES} ${OPENGL_gl_LIBRARY} ${GLEW_LIBRARIES} glfw)
target_link_libraries(sam_webgui_test sam_webgui ${catkin_LIBRARIES} ${SDL2_LIBRARY} ${SDL2_IMAGE_LIBRARIES} ${OPENGL_gl_LIBRARY} ${GLEW_LIBRARIES} glfw)
endif()

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
if (NOT ${ROSWASM_NATIVE})
install(PROGRAMS www/webgui.html ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.wasm
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
endif()

## Mark executables for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_executables.html
install(TARGETS sam_webgui_test
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark libraries for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_libraries.html
install(TARGETS sam_webgui
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

## Mark other files for installation (e.g. launch and bag files, etc.)
install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  PATTERN ".svn" EXCLUDE
)

else() # no emsdk

find_package(catkin REQUIRED COMPONENTS roscpp roscpp_json_serialize std_msgs sensor_msgs geometry_msgs nav_msgs) # rosmon_msgs)
catkin_package()

configure_file(www/webgui.html ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.html COPYONLY)
configure_file(www/webgui.wasm ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.wasm COPYONLY)
configure_file(www/webgui.js ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.js COPYONLY)

endif()
