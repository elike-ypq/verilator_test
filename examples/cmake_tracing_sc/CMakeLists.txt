######################################################################
#
# DESCRIPTION: Verilator CMake Example: Small CMakeLists.txt with SystemC tracing
#
# This is an example cmake script to build a verilog to SystemC project
# using CMake and Verilator.
#
# This file ONLY is placed under the Creative Commons Public Domain, for
# any use, without warranty, 2020 by Wilson Snyder.
# SPDX-License-Identifier: CC0-1.0
#
######################################################################

# This example builds the tracing_sc example using CMake
# To use it, run the following:

# cd /path/to/verilator/examples/cmake_tracing_sc
# rm -rf build && mkdir build && cd build
# cmake ..
# cmake --build .

cmake_minimum_required(VERSION 3.8)
project(cmake_tracing_sc_example CXX)

find_package(verilator HINTS $ENV{VERILATOR_ROOT} ${VERILATOR_ROOT})
if (NOT verilator_FOUND)
  message(FATAL_ERROR "Verilator was not found. Either install it, or set the VERILATOR_ROOT environment variable")
endif()

if(MSVC)
    string(APPEND CMAKE_CXX_FLAGS " /Zc:__cplusplus")
endif()

# SystemC dependencies
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)
# boost package

# set(BOOST_ROOT "C:/aboost/boost_1_78_0")
# set(Boost_ADDITIONAL_VERSIONS "1.78.0" )
# set(Boost_USE_STATIC_LIBS        ON)
# set(Boost_USE_MULTITHREADED      ON)
# set(Boost_USE_STATIC_RUNTIME    OFF)
# find_package(Boost COMPONENTS system filesystem thread)
# if(Boost_FOUND)
    # message(STATUS "boost include path is : ${Boost_INCLUDE_DIRS}")
    # message(STATUS "boost library path is : ${Boost_LIBRARY_DIRS}")
    # message(STATUS "boost libraries is : ${Boost_LIBRARIES}")
    # include_directories(${Boost_INCLUDE_DIRS})
    # link_directories(${Boost_LIBRARY_DIRS})
# else()
    # message(WARNING "boost not found.")
# endif()

# Find SystemC using SystemC's CMake integration
set (CMAKE_PREFIX_PATH $ENV{SYSTEMC_HOME})
find_package(SystemCLanguage CONFIG REQUIRED)

set (CMAKE_CXX_STANDARD ${SystemC_CXX_STANDARD} CACHE STRING
     "C++ standard to build all targets. Supported values are 98, 11, and 14.")
set (CMAKE_CXX_STANDARD_REQUIRED ${SystemC_CXX_STANDARD_REQUIRED} CACHE BOOL
     "The with CMAKE_CXX_STANDARD selected C++ standard is a requirement.")


# Create a new executable target that will contain all your sources
add_executable(example ../make_tracing_sc/sc_main.cpp)

set_property(
  TARGET example
  PROPERTY CXX_STANDARD ${SystemC_CXX_STANDARD}
  )

# Add the Verilated circuit to the target
verilate(example SYSTEMC COVERAGE TRACE
  INCLUDE_DIRS "../make_tracing_sc"
  VERILATOR_ARGS -f ../make_tracing_sc/input.vc -Os -x-assign 0
  SOURCES ../make_tracing_sc/top.v)

verilator_link_systemc(example)
