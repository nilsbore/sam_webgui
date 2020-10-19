cmake_minimum_required(VERSION 2.8.3)
project(sam_webgui)

if (DEFINED ENV{EMSDK})

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS roscpp roswasm_webgui sam_msgs std_msgs sensor_msgs geometry_msgs nav_msgs)

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

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

#add_library(sam_webgui STATIC src/roswasm_sam.cpp)
add_library(sam_webgui src/roswasm_sam.cpp)

add_executable(sam_webgui_test src/main.cpp)
set_target_properties(sam_webgui_test PROPERTIES OUTPUT_NAME "webgui.js")

target_link_libraries(sam_webgui ${roswasm_webgui_LIBRARIES} ${roswasm_LIBRARIES})
target_link_libraries(sam_webgui_test sam_webgui ${roswasm_webgui_LIBRARIES} ${roswasm_LIBRARIES})

configure_file(www/webgui.html ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.html COPYONLY)

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
install(PROGRAMS www/webgui.html ${CATKIN_DEVEL_PREFIX}/${CATKIN_PACKAGE_BIN_DESTINATION}/webgui.wasm
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark executables for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_executables.html
install(TARGETS sam_webgui_test
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark other files for installation (e.g. launch and bag files, etc.)
install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  PATTERN ".svn" EXCLUDE
)

else() # no emsdk

find_package(catkin REQUIRED COMPONENTS roscpp roscpp_json_serialize std_msgs sensor_msgs geometry_msgs nav_msgs) # rosmon_msgs)
catkin_package()

endif()
