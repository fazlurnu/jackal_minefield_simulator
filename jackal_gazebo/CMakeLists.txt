cmake_minimum_required(VERSION 2.8.3)
project(jackal_gazebo)

find_package(catkin REQUIRED COMPONENTS roslaunch cmake_modules gazebo_ros message_generation rosconsole roscpp geometry_msgs sensor_msgs nav_msgs metal_detector_msgs tf)
find_package(gazebo REQUIRED)

catkin_package(
  INCLUDE_DIRS
  LIBRARIES 
  CATKIN_DEPENDS gazebo_ros
  DEPENDS gazebo_ros metal_detector_msgs
)

roslaunch_add_file_check(launch/jackal_world.launch)
roslaunch_add_file_check(launch/world-park.launch)
roslaunch_add_file_check(launch/minefield.launch)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
#include_directories(include ${catkin_INCLUDE_DIRS})
link_directories(${GAZEBO_LIBRARY_DIRS})
include_directories(include ${Boost_INCLUDE_DIR} ${catkin_INCLUDE_DIRS} ${GAZEBO_INCLUDE_DIRS})

## Declare a cpp executable
#add_executable(arm_controller src/arm_controller.cpp)

### Specify libraries to link a library or executable target against
##target_link_libraries(arm_controller ${catkin_LIBRARIES})
#target_link_libraries(gazebo_mimic_plugin ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES})
#target_link_libraries(gazebo_gps_plugin ${GAZEBO_LIBRARIES})

add_executable(gps_to_odom src/gps_to_odom/gps_to_odom.cpp src/UTMConverter/UTMConverter.cpp)
target_link_libraries(gps_to_odom ${catkin_LIBRARIES})

add_executable(minefield_static_tf_publisher src/minefield_static_tf_publisher/minefield_static_tf_publisher.cpp src/UTMConverter/UTMConverter.cpp)
target_link_libraries(minefield_static_tf_publisher ${catkin_LIBRARIES})

add_executable(judge src/judge/main.cpp src/judge/judge.cpp src/config/config.cpp src/config/robotPose.cpp src/config/trueRobotPose.cpp src/UTMConverter/UTMConverter.cpp)
target_link_libraries(judge ${catkin_LIBRARIES})

add_executable(mineFieldViewer src/minefieldViewer/main.cpp src/minefieldViewer/minefieldviewer.cpp src/config/robotPose.cpp src/config/trueRobotPose.cpp src/config/config.cpp src/UTMConverter/UTMConverter.cpp)
target_link_libraries(mineFieldViewer ${catkin_LIBRARIES})

add_executable(coilSignalSimulator src/coilSignalSimulator/main.cpp src/coilSignalSimulator/CoilSignalSimulator.cpp src/config/robotPose.cpp src/config/trueRobotPose.cpp src/config/config.cpp src/UTMConverter/UTMConverter.cpp)
target_link_libraries(coilSignalSimulator ${catkin_LIBRARIES})

add_executable(convert_coordinates src/UTMConverter/convert_coordinates.cpp src/UTMConverter/UTMConverter.cpp)
target_link_libraries(convert_coordinates ${catkin_LIBRARIES})

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
add_dependencies(coilSignalSimulator metal_detector_msgs_generate_messages_py)

##############
## EO Build ##
##############

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
 install(PROGRAMS
   scripts/minefield_to_odom_tf.py
   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
 )

################
## EO Install ##
################

install(DIRECTORY launch Media worlds
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
