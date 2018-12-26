set(the_description "Camera Calibration and 3D Reconstruction")
 
if(EVSS_OPENCV_REDUCE)
ocv_module_disable(calib3d)
else()
ocv_define_module(calib3d opencv_imgproc opencv_features2d)  
endif()
