cmake_minimum_required(VERSION 2.4.6)
project(ccny_rgbd)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  pcl_ros
  nav_msgs
  sensor_msgs
  std_msgs
  tf
  dynamic_reconfigure
  cv_bridge
  sensor_msgs
  geometry_msgs
  visualization_msgs
  image_transport
  image_geometry
  nodelet
  message_generation
)



set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

################################################################
# Dependencies
################################################################

# OpenCV
FIND_PACKAGE( OpenCV REQUIRED )
include_directories(${OpenCV_INCLUDE_DIRS})
link_directories(${OpenCV_LIBRARY_DIRS})
link_libraries(${OpenCV_LIBRARIES})

# Octomap
find_package(octomap REQUIRED)
include_directories(${OCTOMAP_INCLUDE_DIRS})
link_directories(${OCTOMAP_LIBRARY_DIRS})
link_libraries(${OCTOMAP_LIBRARIES})

# PCL
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
link_libraries(${PCL_LIBRARIES})

#g2o
set(G2O_LIBRARIES 
  g2o_core 
  g2o_stuff
  g2o_solver_cholmod 
  g2o_solver_csparse 
  g2o_types_slam3d
  cholmod)

link_directories(/usr/local/lib)


generate_dynamic_reconfigure_options(
  cfg/FeatureDetector.cfg
  cfg/GftDetector.cfg
  cfg/OrbDetector.cfg
  cfg/RGBDImageProc.cfg
  cfg/StarDetector.cfg
  cfg/SurfDetector.cfg
)


include_directories(
  ${PROJECT_SOURCE_DIR}/include
  ${PROJECT_SOURCE_DIR}/cfg/cpp
  ${catkin_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIR}
)


add_service_files(
  FILES
  AddManualKeyframe.srv
  GenerateGraph.srv
  Load.srv
  PublishKeyframe.srv
  PublishKeyframes.srv
  Save.srv
  SolveGraph.srv
)

generate_messages(
  DEPENDENCIES
  std_msgs
)

catkin_package(
)


################################################################
# Build visual odometry application
################################################################

add_executable(visual_odometry_node 
  src/node/visual_odometry_node.cpp
  src/apps/visual_odometry.cpp
  src/util.cpp)
  
target_link_libraries(visual_odometry_node
  ${catkin_LIBRARIES}
  rgbdtools
  boost_signals
  boost_system
  boost_filesystem
  ${OpenCV_LIBRARIES})
add_dependencies(visual_odometry_node ${catkin_EXPORTED_TARGETS} ${PROJECT_NAME}_gencfg)
  

