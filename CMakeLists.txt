cmake_minimum_required(VERSION 3.5)
project(dynamic_reconfigure)
# Add support for C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

find_package(ament_cmake REQUIRED)
#find_package(chrono REQUIRED)
#find_package(rostest REQUIRED)
#find_package(thread REQUIRED)
#find_package(system REQUIRED)
#find_package(boost REQUIRED)
find_package(std_msgs REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rosidl_default_generators REQUIRED)

set(INCLUDE_DIRS include ${ament_cmake_INCLUDE_DIRS} ${rosidl_default_generators_INCLUDE_DIRS} ${std_msgs_INCLUDE_DIRS}
  ${rclcpp_INCLUDE_DIRS} ${REQUIRED_INCLUDE_DIRS})
include_directories(${INCLUDE_DIRS})
set(LIBRARY_DIRS ${ament_cmake_LIBRARIES} ${rosidl_default_generators_LIBRARIES} ${std_msgs_LIBRARIES} ${rclcpp_LIBRARIES}
  ${REQUIRED_LIBRARIES})
set(LIBS ${ament_cmake_LIBRARIES} ${rosidl_default_generators_LIBRARIES}
  ${std_msgs_LIBRARIES} ${rclcpp_LIBRARIES} ${REQUIRED_LIBRARIES})
   
  rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/BoolParameter.msg" "msg/Config.msg" "msg/Group.msg" "msg/IntParameter.msg"
  "msg/SensorLevels.msg" "msg/ConfigDescription.msg" "msg/DoubleParameter.msg"
  "msg/GroupState.msg" "msg/ParamDescription.msg" "msg/StrParameter.msg" 
   "srv/Reconfigure.srv"
  DEPENDENCIES builtin_interfaces std_msgs)

if(CATKIN_ENABLE_TESTING)
  set(dynamic_reconfigure_BASE_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
  include(
    ${dynamic_reconfigure_SOURCE_DIR}/cmake/dynamic_reconfigure-macros.cmake)
 # generate_dynamic_reconfigure_options(cfg/Test.cfg)
 # add_subdirectory(test)
endif()

add_library(dynamic_reconfigure_config_init_mutex
  src/dynamic_reconfigure_config_init_mutex.cpp)

install(DIRECTORY include/dynamic_reconfigure/
  DESTINATION include/${PROJECT_NAME}
  FILES_MATCHING
  PATTERN "*.h")

install(TARGETS dynamic_reconfigure_config_init_mutex
  ARCHIVE
  DESTINATION lib
  LIBRARY
  DESTINATION lib
  RUNTIME
  DESTINATION bin)

install(DIRECTORY cmake
  DESTINATION share/${PROJECT_NAME}
  USE_SOURCE_PERMISSIONS)
install(DIRECTORY msg DESTINATION share/${PROJECT_NAME})
install(PROGRAMS scripts/dynparam DESTINATION bin)
install(PROGRAMS scripts/reconfigure_gui
  DESTINATION bin)
install(DIRECTORY srv DESTINATION share/${PROJECT_NAME})
install(DIRECTORY templates DESTINATION share/${PROJECT_NAME})

ament_export_dependencies(ament_cmake)
#ament_export_dependencies(chrono)
#ament_export_dependencies(rostest)
#ament_export_dependencies(thread)
#ament_export_dependencies(system)
ament_export_dependencies(rosidl_default_generators)
ament_export_dependencies(std_msgs)
ament_export_dependencies(rclcpp)
ament_export_include_directories(${INCLUDE_DIRS})
ament_export_libraries(dynamic_reconfigure_config_init_mutex ${LIBS})

ament_package()
