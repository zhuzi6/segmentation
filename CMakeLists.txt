cmake_minimum_required(VERSION 3.5)
project(seg_test)

# set(CMAKE_CUDA_ARCHITECTURES 60 61 62 70 72 75 86 89 90)
set(CMAKE_CUDA_COMPILER /usr/local/cuda/bin/nvcc)
enable_language(CUDA)

# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -O3")
# set(CMAKE_CXX_STANDARD 17)
set(CMAKE_BUILD_TYPE Release)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)


option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(THIRDPARTY "/opt/galbot/third_party")

# include and link dirs of cuda and tensorrt, you need adapt them if yours are different
if (CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64")
    message("embed_platform on")
    # cuda
    set(CUDA_INCLUDE_DIRS /usr/local/cuda-11.4/targets/aarch64-linux/include)
    set(CUDA_LIBRARIES /usr/local/cuda-11.4/targets/aarch64-linux/lib)
    include_directories(/usr/local/cuda-11.4/targets/aarch64-linux/include)
    link_directories(/usr/local/cuda-11.4/targets/aarch64-linux/lib)

    # TensorRT
    set(TensorRT_INCLUDE_DIRS /usr/include/aarch64-linux-gnu)
    set(TensorRT_LIBRARIES /usr/lib/aarch64-linux-gnu)

    

    # if(NOT EXISTS "${THIRDPARTY}" OR NOT IS_DIRECTORY "${THIRDPARTY}")
    #     set(THIRDPARTY "/opt/galbot/thirdparty/gcc940-aarch64-ubuntu2004-gnu")
    # endif()

else()
    message("embed_platform off")
    # cuda
    set(CUDA_INCLUDE_DIRS /usr/local/cuda/include)
    set(CUDA_LIBRARIES /usr/local/cuda/lib64)
    include_directories(/usr/local/cuda/include)
    link_directories(/usr/local/cuda/lib64)

    # TensorRT  # TensorRT_ROOT的路径设置成自己的
    set(TensorRT_ROOT ~/ENV/TensorRT-8.5.2.2)
    set(TensorRT_INCLUDE_DIRS ${TensorRT_ROOT}/include)
    set(TensorRT_LIBRARIES ${TensorRT_ROOT}/targets/x86_64-linux-gnu/lib)

    # if(NOT EXISTS "${THIRDPARTY}" OR NOT IS_DIRECTORY "${THIRDPARTY}")
    #     set(THIRDPARTY "/opt/galbot/thirdparty/gcc940-x86_64-ubuntu2004-gnu")
    # endif()

endif()

# 包含头文件的目录
include_directories(
    common 
    # ${THIRDPARTY}/include
    # ${OpenCV_INCLUDE_DIRS}
    ${CUDA_INCLUDE_DIRS}
    ${TensorRT_INCLUDE_DIRS}
    )

set(DEPS  
    # ${OpenCV_LIBS} 
    # ${PCL_LIBRARIES} 
    # ${ZBAR_LIBRARIES}
    nvinfer
    nvinfer_plugin
    cudart
    # glog
    )  
link_directories(
    ${CUDA_LIBRARIES}
    ${TensorRT_LIBRARIES} 
    # ${THIRDPARTY}/lib
)

add_executable(demo_seg
    main.cpp
    common/logger.cpp
    common/util.cpp
)
target_link_libraries(demo_seg ${DEPS})