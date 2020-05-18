cmake_minimum_required(VERSION 2.8.3)
project(sam_webgui)

set(CMAKE_C_COMPILER emcc)
set(CMAKE_CXX_COMPILER em++)

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
#  DEPENDS system_lib
)

###########
## Build ##
###########

# setting EMSCRIPTEN_GENERATE_BITCODE_STATIC_LIBRARIES=ON here does not
# work, since it will only be taken into account on the next cmake
# configuration. So we explicitly run the code guarded by
# EMSCRIPTEN_GENERATE_BITCODE_STATIC_LIBRARIES=ON in Emscripten.cmake
# platform file.
set(CMAKE_STATIC_LIBRARY_SUFFIX ".bc")
set(CMAKE_C_CREATE_STATIC_LIBRARY "<CMAKE_C_COMPILER> -o <TARGET> <LINK_FLAGS> <OBJECTS>")
set(CMAKE_CXX_CREATE_STATIC_LIBRARY "<CMAKE_CXX_COMPILER> -o <TARGET> <LINK_FLAGS> <OBJECTS>")
# --- end EMSCRIPTEN_GENERATE_BITCODE_STATIC_LIBRARIES

set(EMCC_FLAGS "")
#set(EMCC_FLAGS "${EMCC_FLAGS} -s DISABLE_EXCEPTION_CATCHING=0")
set(EMCC_FLAGS "${EMCC_FLAGS} -s ERROR_ON_UNDEFINED_SYMBOLS=1")
set(EMCC_FLAGS "${EMCC_FLAGS} -s ALLOW_MEMORY_GROWTH=1")
#set(EMCC_FLAGS "${EMCC_FLAGS} -s ASSERTIONS=1")
set(EMCC_FLAGS "${EMCC_FLAGS} -s WASM=1")
set(EMCC_FLAGS "${EMCC_FLAGS} -Wno-warn-absolute-paths")
#set(EMCC_FLAGS "${EMCC_FLAGS} --memory-init-file 0")
set(EMCC_FLAGS "${EMCC_FLAGS} -s USE_BOOST_HEADERS=1")

set(EMCC_FLAGS "${EMCC_FLAGS} -s USE_WEBGL2=1")
set(EMCC_FLAGS "${EMCC_FLAGS} -s USE_GLFW=3")
set(EMCC_FLAGS "${EMCC_FLAGS} -s FULL_ES3=1")
set(EMCC_FLAGS "${EMCC_FLAGS} -s USE_SDL=2")
set(EMCC_FLAGS "${EMCC_FLAGS} -s USE_SDL_IMAGE=2")
set(EMCC_FLAGS "${EMCC_FLAGS} -s SDL2_IMAGE_FORMATS='[\"png\", \"jpg\"]'")
#set(EMCC_FLAGS "${EMCC_FLAGS} --preload-file data")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${EMCC_FLAGS}")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${EMCC_FLAGS}")

add_definitions("-g4 -O2 -std=c++11")

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

add_library(sam_webgui STATIC src/roswasm_sam.cpp)

add_executable(sam_webgui_test src/main.cpp)
set_target_properties(sam_webgui_test PROPERTIES OUTPUT_NAME "webgui.js")

target_link_libraries(sam_webgui roswasm_webgui roswasm -lGL -lwebsocket.js)
target_link_libraries(sam_webgui_test sam_webgui roswasm_webgui roswasm -lGL -lwebsocket.js)
