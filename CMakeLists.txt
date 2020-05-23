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

add_library(sam_webgui STATIC src/roswasm_sam.cpp)

add_executable(sam_webgui_test src/main.cpp)
set_target_properties(sam_webgui_test PROPERTIES OUTPUT_NAME "webgui.js")

target_link_libraries(sam_webgui ${roswasm_webgui_LIBRARIES} ${roswasm_LIBRARIES})
target_link_libraries(sam_webgui_test sam_webgui ${roswasm_webgui_LIBRARIES} ${roswasm_LIBRARIES})

else() # no emsdk

find_package(catkin REQUIRED COMPONENTS roscpp roscpp_json_serialize std_msgs sensor_msgs geometry_msgs nav_msgs) # rosmon_msgs)
catkin_package()

endif()
