if(BUILD_ANDROID_PACKAGE)
  ocv_module_disable(nonfree)
endif()


set(the_description "Functionality with possible limitations on the use")
ocv_warnings_disable(CMAKE_CXX_FLAGS -Wundef -Wshadow)
if(ENABLE_DYNAMIC_CUDA)
  add_definitions(-DDYNAMIC_CUDA_SUPPORT)
  ocv_define_module(nonfree EXCLUDE_CUDA opencv_imgproc opencv_features2d opencv_calib3d OPTIONAL opencv_ocl)
else()
  ocv_define_module(nonfree opencv_imgproc opencv_features2d opencv_calib3d OPTIONAL opencv_gpu opencv_ocl)
endif()
