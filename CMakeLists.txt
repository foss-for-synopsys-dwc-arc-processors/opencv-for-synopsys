cmake_minimum_required(VERSION 3.8 FATAL_ERROR)
include(evrt)

list(APPEND as_subproject evthreads includes config evmss evdev)

# Add minimal subset of EVRT packages here so that OpenCV can build
if (NOT HOST)
add_subdirectory(${EVSS_RT_HOME}/drivers/mss "ev_runtime/software/drivers/mss")
add_subdirectory(${EVSS_RT_HOME}/config "ev_runtime/software/config")
add_subdirectory(${EVSS_RT_HOME}/include "ev_runtime/software/include")
add_subdirectory(${EVSS_RT_HOME}/evthreads "ev_runtime/software/evthreads")
add_subdirectory(${EVSS_RT_HOME}/drivers/hal "ev_runtime/software/drivers/hal")
endif()
add_subdirectory("2.4.9")
