cmake_minimum_required(VERSION 3.0.2)
project(rviz_satellite)

find_package(catkin REQUIRED
  COMPONENTS
    roscpp
    rviz
    sensor_msgs
)

catkin_package(
  CATKIN_DEPENDS
    roscpp
    rviz
    sensor_msgs
)

find_package(Qt5 ${rviz_QT_VERSION} EXACT REQUIRED Core Gui Network REQUIRED)
set(QT_LIBRARIES Qt5::Core Qt5::Gui Qt5::Network)


## BUILD ##

set(rviz_satellite_SOURCES
  src/aerialmap_display.cpp
  src/ogre_tile.cpp
  src/tile_id.cpp
)

set(rviz_satellite_HEADERS
  src/aerialmap_display.h
  src/detail/tile_downloader.h
)

# invoke MOC and UI/ include Qt headers/ link Qt libraries - alternatively, see CMAKE_AUTOMOC
qt5_wrap_cpp(rviz_satellite_MOC_FILES ${rviz_satellite_HEADERS})

set(PROJECT_SOURCE_FILES
  ${rviz_satellite_SOURCES}
  ${rviz_satellite_MOC_FILES}
)

include_directories(
  src
  ${catkin_INCLUDE_DIRS}
)

add_library(
  rviz_satellite
  ${PROJECT_SOURCE_FILES}
)

# set compiler options and inject version of software (see tile_downloader.h)
# rviz_satellite_VERSION is defined via catkin and is read from the package.xml
target_compile_options(
  rviz_satellite
  PUBLIC
  -Wall -Wunused -std=c++11 "-DRVIZ_SATELLITE_VERSION=\"${rviz_satellite_VERSION}\""
)

target_link_libraries(
  rviz_satellite
  ${QT_LIBRARIES}
  ${catkin_LIBRARIES}
)


## INSTALL ##

install(TARGETS
  rviz_satellite
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(FILES
  plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
