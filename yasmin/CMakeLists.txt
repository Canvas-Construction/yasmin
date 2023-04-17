cmake_minimum_required(VERSION 3.5)
project(yasmin)

# Find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclpy REQUIRED)
find_package(Boost 1.80 COMPONENTS system)

set(LIB ${CMAKE_PROJECT_NAME}_lib)
set(SOURCES
  src/yasmin/blackboard/blackboard.cpp
  src/yasmin/state.cpp
  src/yasmin/cb_state.cpp
  src/yasmin/state_machine.cpp
)

add_library(${LIB} STATIC ${SOURCES})

target_link_libraries(${LIB} 
  PUBLIC 
    Boost::boost 
)

target_compile_features(${LIB} 
  PUBLIC 
    cxx_std_14
)

target_compile_options(${LIB} 
  PUBLIC 
    $<$<CXX_COMPILER_ID:MSVC>:/permissive- /W4 /MP>
    $<$<NOT:$<CXX_COMPILER_ID:MSVC>>:-Wall -Wextra -Wpedantic>
)

target_include_directories(${LIB} 
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

install(
  TARGETS ${LIB}
  EXPORT ${LIB}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(DIRECTORY include/
  DESTINATION include/
)

ament_export_targets(${LIB} HAS_LIBRARY_TARGET)

# Python
ament_python_install_package(${PROJECT_NAME})

# Tests
install(
  DIRECTORY tests/python/
  DESTINATION pytests/
)

install(
  PROGRAMS tests/python/run_pytests.sh
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()
