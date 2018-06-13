cmake_minimum_required(VERSION 2.8.3)
project(apriltags)

# Set up the ROS Catkin package settings.
set(CATKIN_PACKAGES
  cv_bridge
  std_msgs
  sensor_msgs
  image_transport
  roscpp
  geometry_msgs
)

find_package(catkin REQUIRED COMPONENTS ${CATKIN_PACKAGES} message_generation)

find_package(cmake_modules REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Eigen3 REQUIRED)
set(Eigen3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})

# apriltag cpp
find_package(apriltagscpp REQUIRED)


# Import the yaml-cpp libraries.
include(FindPkgConfig)
pkg_check_modules(Yaml REQUIRED yaml-cpp)

add_message_files(DIRECTORY msg FILES
    AprilTagDetection.msg
    AprilTagDetections.msg
)

generate_messages(DEPENDENCIES
    std_msgs
    geometry_msgs
)

# Set up the ROS Catkin package settings
catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS ${CATKIN_PACKAGES} message_runtime
  DEPENDS Eigen3
)

# CGAL requires that -frounding-math be set.
add_definitions(-frounding-math)

include_directories(
    include/
    SYSTEM
    ${catkin_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
    ${Yaml_INCLUDE_DIRS}
    ${apriltagscpp_INCLUDE_DIR}
)

add_executable(apriltags src/apriltags.cpp)
target_link_libraries(apriltags ${catkin_LIBRARIES}
                                ${Eigen_LIBRARIES}
                                ${OpenCV_LIBRARIES}
                                ${Yaml_LIBRARIES}    
                                ${apriltagscpp_LIBRARY})
add_dependencies(apriltags ${PROJECT_NAME}_gencpp)

install(TARGETS apriltags
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION} 
)

#install(FILES ${apriltagscpp_LIBRARY}
#  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#)

