cmake_minimum_required(VERSION 3.5)
project(reinforcement_learning)

set(CMAKE_CXX_STANDARD 11)

option(BUILD_PYTHON "Build the Python bindings" OFF)

# TODO Compile cpprest into its own separate lib so that it doesn't have to be consumed
find_package(cpprestsdk REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(Boost COMPONENTS unit_test_framework system program_options REQUIRED)

set(CMAKE_CONFIGURATION_TYPES Debug Release CACHE TYPE INTERNAL FORCE)
set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "Choose the type of build, options are: Debug, Release" FORCE)

# Devirtualization is an optimization that changes the vtable if the compiler decides a function
# doesn't need to be virtual. This is incompatible with the mocking framework used in testing as it
# makes the vtable structure unpredictable
option(TURN_OFF_DEVIRTUALIZE "Turn off devirtualize which is required for mocking framework" OFF)

include(ProcessorCount)
ProcessorCount(NumProcessors)
message("Number of processors: ${NumProcessors}")
file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/nprocs.txt ${NumProcessors})

add_subdirectory(bindings EXCLUDE_FROM_ALL)
add_subdirectory(doc EXCLUDE_FROM_ALL)
add_subdirectory(ext_libs)
add_subdirectory(rlclientlib)
add_subdirectory(examples)

# enable_testing should be run after ext_libs so that the vw unit tests arent turned on.
enable_testing()
add_subdirectory(unit_test)
