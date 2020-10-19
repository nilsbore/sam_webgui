# sam_webgui
![CI](https://github.com/nilsbore/sam_webgui/workflows/CI/badge.svg?branch=master) [![license](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)

Dashboard interface for the SAM AUV based on roswasm_webgui

## Running the interface

Run using `roslaunch sam_webgui webgui.launch` and navigate to `1.2.3.4:8080` in a browser (preferrably chrome),
where `1.2.3.4` should be replaced by the IP of the machine that the interface was launched on.

## Dependencies for running (on SAM or elsewhere)

* [roswasm_suite](https://github.com/nilsbore/roswasm_suite): clone in catkin workspace with `git clone https://github.com/nilsbore/roswasm_suite.git`
* [rosbridge_suite](https://github.com/RobotWebTools/rosbridge_suite) after [the commit adding cbor-raw compression](https://github.com/RobotWebTools/rosbridge_suite/commit/dc7fcb282d1326d573abe83579cc7d989ae71739), latest `develop` should do:
  ```bash
  git clone https://github.com/RobotWebTools/rosbridge_suite -b develop # in workspace src folder
  ```
  You can build the workspace as you would otherwise after this, but it won't really do much since the interface is already compiled
  and can be used as-is. You do not need to do any of the following steps if you just want to run the interface.

## Dependencies when developing

* [Emscripten](https://emscripten.org/docs/getting_started/downloads.html) tested with version `1.39.10` but latest should do
* [roswasm_suite](https://github.com/nilsbore/roswasm_suite): clone in catkin workspace with `git clone https://github.com/nilsbore/roswasm_suite.git`
* [rosbridge_suite](https://github.com/RobotWebTools/rosbridge_suite) after [the commit adding cbor-raw compression](https://github.com/RobotWebTools/rosbridge_suite/commit/dc7fcb282d1326d573abe83579cc7d989ae71739), latest `develop` should do:
  ```bash
  git clone https://github.com/RobotWebTools/rosbridge_suite -b develop # in workspace src folder
  ```

## Building when developing

`catkin build` is recommended, since `catkin_make` might leak configurations to other packages.
Make sure to [source the Emscripten environment](https://emscripten.org/docs/getting_started/downloads.html#installation-instructions)
before building the package:
```bash
source /path/to/emsdk/emsdk_env.sh
catkin build
```
