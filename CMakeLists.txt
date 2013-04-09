cmake_minimum_required(VERSION 2.6)

project(sandbox CXX)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Debug")
endif()

set(CXX_FLAGS
 -g
 # -DVALGRIND
 # -DMUDUO_STD_STRING
 -D_FILE_OFFSET_BITS=64
 -Wall
 -Wextra
 #-Werror
 -Wconversion
 -Wno-unused-parameter
 #-Wold-style-cast
 -Woverloaded-virtual
 -Wpointer-arith
 #-Wshadow
 -std=c++0x
 )

set(CMAKE_CXX_COMPILER "g++")
set(CMAKE_CXX_FLAGS_DEBUG "-O0")
set(EXECUTABLE_OUTPUT_PATH bin)
set(LIBRARY_OUTPUT_PATH lib)

include_directories(
    /opt/boost/include/
    /opt/protobuf/include/
    /opt/lua/include/
    )

#list(APPEND CMAKE_LIBRARY_PATH /opt/lua/lib/)

link_directories(
    /opt/lua/lib
    )

set(LIBRARIES
    lua
    boost_unit_test_framework
    )

if(CMAKE_BUILD_BITS EQUAL 32)
  list(APPEND CXX_FLAGS "-m32")
endif()
string(REPLACE ";" " " CMAKE_CXX_FLAGS "${CXX_FLAGS}")

find_package(Boost REQUIRED)
find_package(Protobuf)
if(Boost_FOUND)
    message("boost_include_dir = " ${Boost_INCUDE_DIR})
    message("boost_library = " ${Boost_LIBRARY})
endif()

include_directories(${Boost_INCLUDE_DIRS})
include_directories(${PROJECT_SOURCE_DIR})

string(TOUPPER ${CMAKE_BUILD_TYPE} BUILD_TYPE)
message(STATUS "CXX_FLAGS = " ${CMAKE_CXX_FLAGS} " " ${CMAKE_CXX_FLAGS_${BUILD_TYPE}})

file(GLOB SOURCES "*.cc")
foreach(SOURCE IN LISTS SOURCES)
  GET_FILENAME_COMPONENT(output ${SOURCE} NAME_WE)
  add_executable(${output} ${SOURCE})
  target_link_libraries(${output} ${LIBRARIES})
endforeach()
