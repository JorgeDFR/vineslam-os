cmake_minimum_required(VERSION 3.5)
project(vineslam_ros)

# Default to C++17
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_BUILD_TYPE Release)

option(LIDAR_TYPE "Type of Lidar" 0)
add_definitions(-DLIDAR_TYPE=${LIDAR_TYPE})

find_package(ament_cmake REQUIRED)
find_package(ament_index_cpp REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(rclcpp REQUIRED)
find_package(vineslam REQUIRED)
find_package(std_msgs REQUIRED)
find_package(std_srvs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(visualization_msgs REQUIRED)
find_package(interactive_markers REQUIRED)
find_package(vision_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(OpenCV REQUIRED)
find_package(OpenCV REQUIRED)
find_package(ublox_msgs REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(PCL REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
        "srv/SaveMap.srv"
        DEPENDENCIES std_msgs)

include_directories(
        include
        ${topological_map_io_INCLUDE_DIRS}
        ${vineslam_INCLUDE_DIRS})

set(dependencies
        "ament_index_cpp"
        "vineslam"
        "rclcpp"
        "std_srvs"
        "std_msgs"
        "sensor_msgs"
        "nav_msgs"
	"vision_msgs"
        "visualization_msgs"
        "interactive_markers"
        "tf2"
        "tf2_ros"
        "tf2_geometry_msgs"
        "geometry_msgs"
        "ublox_msgs"
        "pcl_conversions")

add_executable(slam_node src/slam_node.cpp src/vineslam_ros.cpp src/visualization.cpp)
ament_target_dependencies(slam_node ${dependencies})
rosidl_target_interfaces(slam_node ${PROJECT_NAME} "rosidl_typesupport_cpp")
target_link_libraries(slam_node vineslam::vineslam ${OpenCV_LIBRARIES} ${PCL_LIBRARIES} stdc++fs)

add_executable(localization_node src/localization_node.cpp src/vineslam_ros.cpp src/visualization.cpp)
ament_target_dependencies(localization_node ${dependencies})
rosidl_target_interfaces(localization_node ${PROJECT_NAME} "rosidl_typesupport_cpp")
target_link_libraries(localization_node vineslam::vineslam ${OpenCV_LIBRARIES} ${PCL_LIBRARIES})

install(TARGETS
        slam_node
        localization_node
        DESTINATION lib/${PROJECT_NAME})

ament_package()
