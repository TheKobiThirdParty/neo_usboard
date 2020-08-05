cmake_minimum_required(VERSION 2.8.3)
project(neo_usboard)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  sensor_msgs
  neo_msgs
)


###################################
## catkin specific configuration ##
###################################

catkin_package(
  INCLUDE_DIRS common/include
  LIBRARIES SerialIO_USBoard ${PROJECT_NAME}
#  CATKIN_DEPENDS message_runtime
#  DEPENDS system_lib
)

###########
## Build ##
###########
include_directories(common/include ${Boost_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})

add_library(SerialIO_USBoard common/src/SerialIO.cpp)
add_library(${PROJECT_NAME} common/src/SerUSBoard.cpp common/src/neo_usboard_lib.cpp)

add_dependencies(${PROJECT_NAME} neo_msgs_gencpp)
add_dependencies(SerialIO_USBoard neo_msgs_gencpp)

target_link_libraries(${PROJECT_NAME} SerialIO_USBoard)

add_executable(neo_usboard_node src/neo_usboard_node.cpp)
target_link_libraries(neo_usboard_node ${PROJECT_NAME})
target_link_libraries(neo_usboard_node ${catkin_LIBRARIES})
add_dependencies(neo_usboard_node neo_msgs_gencpp)

#############
## Install ##
#############

install(DIRECTORY common/include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(TARGETS ${PROJECT_NAME} SerialIO_USBoard
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})

#############
## Testing ##
#############
