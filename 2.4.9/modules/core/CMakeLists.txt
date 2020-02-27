set(the_description "The Core Functionality")

# Add evthreads to OpenCV dependencies; this is required for being able to
# export OpenCV meta-package, but only works if all dependencies are within
# the same set of CMake Files (i.e. top-level build)
# Therefore, we need to check if evthreads is inside the "as_subproject" list
# (using old list (FIND) syntax, because OpenCV uses old CMake) and revert to
# the old hack in OpenCVModules.cmake if evthreads is not a subproject.
list (FIND as_subproject "evthreads" evthreads_as_subproject)
if(${evthreads_as_subproject} GREATER -1)
set(evthreads_module "evthreads")
include (evrt)
if (NOT DEFINED HOST)
find_package(evthreads CONFIG)
list(APPEND OpenCVModules_TARGETS evthreads evdev includes config)
if (DEFINED ARC)
list(APPEND OpenCVModules_TARGETS crt)
endif(DEFINED ARC)
endif(NOT DEFINED HOST)
endif()

if (NOT HAVE_CUDA OR ENABLE_DYNAMIC_CUDA)
if (DEFINED HOST)
  ocv_add_module(core PRIVATE_REQUIRED ${ZLIB_LIBRARIES})
else(DEFINED HOST)
  ocv_add_module(core PRIVATE_REQUIRED ${ZLIB_LIBRARIES} ${evthreads_module})
endif(DEFINED HOST)
else()
  ocv_add_module(core PRIVATE_REQUIRED ${ZLIB_LIBRARIES} ${CUDA_LIBRARIES} ${CUDA_npp_LIBRARY})
endif()

ocv_module_include_directories("${OpenCV_SOURCE_DIR}/modules/dynamicuda/include/" ${ZLIB_INCLUDE_DIR})

if(HAVE_WINRT_CX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /ZW")
endif()
if(HAVE_WINRT)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /GS /Gm- /AI\"${WINDOWS_SDK_PATH}/References/CommonConfiguration/Neutral\" /AI\"${VISUAL_STUDIO_PATH}/vcpackages\"")
endif()

if(ENABLE_DYNAMIC_CUDA)
  add_definitions(-DDYNAMIC_CUDA_SUPPORT)
else()
  if (HAVE_CUDA)
    add_definitions(-DUSE_CUDA)
  endif()
endif()

if(HAVE_CUDA)
  ocv_include_directories("${OpenCV_SOURCE_DIR}/modules/gpu/include")
  ocv_warnings_disable(CMAKE_CXX_FLAGS -Wundef -Wshadow)
endif()

file(GLOB lib_cuda_hdrs        "include/opencv2/${name}/cuda/*.hpp"        "include/opencv2/${name}/cuda/*.h")
file(GLOB lib_cuda_hdrs_detail "include/opencv2/${name}/cuda/detail/*.hpp" "include/opencv2/${name}/cuda/detail/*.h")

if(HAVE_CUDA AND NOT ENABLE_DYNAMIC_CUDA)
  file(GLOB lib_cuda           "../dynamicuda/src/cuda/*.cu*")
  ocv_include_directories(${CUDA_INCLUDE_DIRS})
  ocv_cuda_compile(cuda_objs ${lib_cuda})
endif()

source_group("Cuda Headers"         FILES ${lib_cuda_hdrs})
source_group("Cuda Headers\\Detail" FILES ${lib_cuda_hdrs_detail})

if (HAVE_CUDA AND NOT ENABLE_DYNAMIC_CUDA)
  source_group("Src\\Cuda"      FILES ${lib_cuda} ${lib_cuda_hdrs})
endif()

if (NOT HAVE_CUDA OR ENABLE_DYNAMIC_CUDA)
  ocv_glob_module_sources(SOURCES "${opencv_core_BINARY_DIR}/version_string.inc"
                          HEADERS ${lib_cuda_hdrs} ${lib_cuda_hdrs_detail})
else()
  ocv_glob_module_sources(SOURCES "${opencv_core_BINARY_DIR}/version_string.inc" ${lib_cuda} ${cuda_objs}
                          HEADERS ${lib_cuda_hdrs} ${lib_cuda_hdrs_detail})
endif()

ocv_create_module()
ocv_add_precompiled_headers(${the_module})

message(WARNING  "BUILD_MODULE  ${BUILD_MODULE}_____")
if ("${BUILD_MODULE}" MATCHES "core|ALL")
message(WARNING "++++++++++++++++ BUILD CORE TESTS ++++++++++")
ocv_add_accuracy_tests()
endif()

if(EVSS_OPENCV_PERF)
   ocv_add_perf_tests()
endif() 
 
