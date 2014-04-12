cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(PCL_KinectKinfu)

SET_PROPERTY(GLOBAL PROPERTY TARGET_SUPPORTS_SHARED_LIBS TRUE)

IF(WIN32 OR win64)
   SET(CMAKE_FIND_LIBRARY_SUFFIXES .lib .dll)
ELSE()
   SET(CMAKE_FIND_LIBRARY_SUFFIXES .a)
ENDIF()


find_package(PCL 1.7 REQUIRED)
find_package( OpenCV REQUIRED )
FIND_PACKAGE(CUDA REQUIRED)
 
SET(CUDA_NVCC_FLAGS "-arch=sm_20" CACHE STRING "nvcc flags" FORCE)
#SET (CUDA_VERBOSE_BUILD ON CACHE BOOL "nvcc verbose" FORCE)



add_subdirectory(src)

set(KinectSDK_INCLUDE "default value" CACHE PATH "Include directories for Kinect2 SDK")
set(KinectSDK_LIB "default value" CACHE PATH "Include directories for Kinect2 SDK")

include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(${PCL_INCLUDE_DIRS})
include_directories(${PCL_KinectKinfu_SOURCE_DIR}/include)
include_directories(../PCL_Kinect2SDK/include)
include_directories(${CUDA_TOOLKIT_INCLUDE})
include_directories(${KinectSDK_INCLUDE})

link_directories(${PCL_LIBRARY_DIRS})
link_directories(../../PCL_Kinect2SDK/build/)
link_directories(${KinectSDK_LIB})

add_definitions(${PCL_DEFINITIONS})

file(GLOB_RECURSE PCL_KinectKinfu_HEADERS include/*.h)
file(GLOB_RECURSE PCL_KinectKinfu_SOURCES src/*.cpp)
set (PCL_KinectKinfu_INCLUDE_DIRS "")
foreach (_headerFile ${PCL_KinectKinfu_HEADERS})
    get_filename_component(_dir ${_headerFile} PATH)
    list (APPEND PCL_KinectKinfu_INCLUDE_DIRS ${_dir})
endforeach()
list(REMOVE_DUPLICATES PCL_KinectKinfu_INCLUDE_DIRS)

include_directories(${PCL_KinectKinfu_INCLUDE_DIRS})

add_executable (PCL_KinectKinfu ${PCL_KinectKinfu_SOURCES} ${PCL_KinectKinfu_HEADERS})
target_link_libraries (PCL_KinectKinfu ${CUDA_CUDA_LIBRARY} ${CUDA_CUDART_LIBRARY} ${PCL_LIBRARIES} ${OpenCV_LIBS} Kinect20.lib debug Debug/PCL_Kinect2SDK optimized Release/PCL_Kinect2SDK)