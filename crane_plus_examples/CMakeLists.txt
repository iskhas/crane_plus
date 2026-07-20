cmake_minimum_required(VERSION 3.20)
project(crane_plus_examples)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 20)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(moveit_ros_planning_interface REQUIRED)
find_package(rclcpp REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(image_geometry REQUIRED)
find_package(OpenCV REQUIRED)

# Build and install node executables
set(executable_list
  gripper_control
  pose_groupstate
  joint_values
  pick_and_place
  pick_and_place_tf
  color_detection
  aruco_detection
)
foreach(loop_var IN LISTS executable_list)
  add_executable(${loop_var} src/${loop_var}.cpp)
  target_link_libraries(${loop_var}
    cv_bridge::cv_bridge
    geometry_msgs::geometry_msgs__rosidl_typesupport_cpp
    moveit_ros_planning_interface::moveit_ros_planning_interface
    ${OpenCV_LIBS}
    rclcpp::rclcpp
    tf2_geometry_msgs::tf2_geometry_msgs
    image_geometry::image_geometry
  )

  install(TARGETS
    ${loop_var}
    DESTINATION lib/${PROJECT_NAME}
  )
endforeach()


install(DIRECTORY
  launch
  config
  DESTINATION share/${PROJECT_NAME}/
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
