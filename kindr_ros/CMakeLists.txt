cmake_minimum_required(VERSION 3.5)
project(kindr_ros)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# Find dependencies
find_package(ament_cmake REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2 REQUIRED)
find_package(kindr REQUIRED)
find_package(Eigen3 REQUIRED)

set(dependencies
  "geometry_msgs"
  "tf2_ros"
  "tf2"
  "kindr"
  "Eigen3"
)

include_directories(include)

set(library_name "${PROJECT_NAME}_lib")

add_library(${library_name} INTERFACE)

target_include_directories(${library_name} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

ament_target_dependencies(${library_name} INTERFACE
  ${dependencies}
)

install(TARGETS ${library_name}
  EXPORT "export_${PROJECT_NAME}"
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib
  INCLUDES DESTINATION include
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
)
if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)

  find_package(ament_cmake_gtest REQUIRED)
  ament_add_gtest(test_${PROJECT_NAME}
    test/test_main.cpp
    test/RosGeometryMsgPhysicalQuantitiesTest.cpp
    test/RosGeometryMsgRotationTest.cpp
    test/RosGeometryMsgPoseTest.cpp
    # test/RosTfPoseTest.cpp
    # test/TfConventionTest.cpp
  )
  # target_link_libraries(test_${PROJECT_NAME}
  #   ${PROJECT_NAME}
  # )
  target_include_directories(test_${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  )
  ament_target_dependencies(test_${PROJECT_NAME}
    ${dependencies}
  )
endif()

ament_export_include_directories(include)
ament_export_dependencies(${dependencies})
ament_export_targets("export_${PROJECT_NAME}")

ament_package()
