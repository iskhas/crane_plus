cmake_minimum_required(VERSION 3.20)
project(crane_plus_control)

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
find_package(dynamixel_sdk REQUIRED)
find_package(hardware_interface REQUIRED)
find_package(pluginlib REQUIRED)
find_package(rclcpp REQUIRED)

## COMPILE
set(LIBRARY_NAME "crane_plus_hardware")
add_library(
  ${LIBRARY_NAME}
  SHARED
  src/crane_plus_hardware.cpp src/crane_plus_driver.cpp
)
target_include_directories(
  ${LIBRARY_NAME}
  PRIVATE
  include
)
target_link_libraries(
  ${LIBRARY_NAME}
  dynamixel_sdk::dynamixel_sdk
  hardware_interface::hardware_interface
  pluginlib::pluginlib
  rclcpp::rclcpp
)

pluginlib_export_plugin_description_file(hardware_interface crane_plus_hardware.xml)

# INSTALL
install(
  TARGETS ${LIBRARY_NAME}
  DESTINATION lib
)
install(
  DIRECTORY include/
  DESTINATION include
)

install(DIRECTORY
  launch
  config
  DESTINATION share/${PROJECT_NAME}/
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

## EXPORTS
ament_export_include_directories(
  include
)
ament_export_libraries(
  ${LIBRARY_NAME}
)
ament_export_dependencies(
  dynamixel_sdk
  hardware_interface
  pluginlib
  rclcpp
)

ament_package()
