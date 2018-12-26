set(the_description "Machine Learning")

if(EVSS_OPENCV_REDUCE)
ocv_module_disable(ml)
else()
ocv_define_module(ml opencv_core)  
endif()
