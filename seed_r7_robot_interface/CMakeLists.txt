cmake_minimum_required(VERSION 2.8.3)
project(seed_r7_robot_interface)

# Dependencies

# catkin ROS
find_package(catkin REQUIRED COMPONENTS
  rospy roscpp tf std_msgs sensor_msgs roslib
  trajectory_msgs geometry_msgs nav_msgs control_msgs
  move_base_msgs
  message_generation
  seed_r7_description
  seed_smartactuator_sdk
)
if(NOT catkin_LIBRARIES)
  message(FATAL "catkin required but not supported")
endif()

# boost
find_package(Boost REQUIRED COMPONENTS filesystem system signals thread)
if(NOT Boost_INCLUDE_DIRS)
  message(FATAL "boost required but not supported")
else()
  include_directories(include ${Boost_INCLUDE_DIRS})
endif()



# ROS setups

catkin_package(
  CATKIN_DEPENDS
  roscpp tf std_msgs sensor_msgs roslib
  trajectory_msgs geometry_msgs nav_msgs control_msgs move_base_msgs
  seed_r7_description
  DEPENDS
  INCLUDE_DIRS include
  LIBRARIES seed_r7_controllers
  seed_smartactuator_sdk
)



include_directories(${catkin_INCLUDE_DIRS})
# add c++ dependencies :
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c+11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
  add_definitions(-DCXX11_SUPPORTED)
elseif(COMPILER_SUPPORTS_CXX0X)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
  add_definitions(-DCXX11_SUPPORTED)
else()
  message(FATAL "c++11 required but not supported")
endif()

add_library(seed_r7_controllers
  src/robot_stroke_converter.cpp
  src/stroke_to_angle.cpp
  src/angle_to_stroke.cpp
  src/seed_r7_upper_controller.cpp
  src/seed_r7_lower_controller.cpp
  )
target_link_libraries(seed_r7_controllers ${catkin_LIBRARIES} ${Boost_LIBRARIES})
