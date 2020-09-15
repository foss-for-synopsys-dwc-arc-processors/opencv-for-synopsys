cmake_minimum_required(VERSION 3.16 FATAL_ERROR)
include(evrt)


# Add minimal subset of EVRT packages here so that OpenCV can build
list(APPEND as_subproject includes config)
add_subdirectory(${EVSS_RT_HOME}/config "../ev_runtime/software/config")
add_subdirectory(${EVSS_RT_HOME}/include "../ev_runtime/software/include")

if (NOT HOST)
list(APPEND as_subproject evthreads includes config evmss evdev evpdm)
add_subdirectory(${EVSS_RT_HOME}/drivers/mss "../ev_runtime/software/drivers/mss")
add_subdirectory(${EVSS_RT_HOME}/drivers/pdm "../ev_runtime/software/drivers/pdm")
add_subdirectory(${EVSS_RT_HOME}/evthreads "../ev_runtime/software/evthreads")
add_subdirectory(${EVSS_RT_HOME}/drivers/hal "../ev_runtime/software/drivers/hal")
endif()
add_subdirectory("2.4.9")
