# Needs CUDAHOSTCXX=/usr/bin/cuda-g++;CUDA_LIB_PATH=/usr/local/cuda/lib64 in
# CLion Settings | Build, Execution, Deployment | CMake > Environment on fedora
# and CUDAHOSTCXX=/usr/bin/g++-8 on pop_os

cmake_minimum_required(VERSION 3.16)

project(CudaSamples LANGUAGES CXX CUDA)
#set(ENV{MPI_HOME} /usr/local )
set(CMAKE_CUDA_STANDARD 14)
set(CMAKE_CUDA_FLAGS ${CMAKE_CUDA_FLAGS} "-arch=sm_75")
set(cuda_include_dirs = ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES})
set(cuda_samples_include = "${cuda_include_dirs}/../../../samples/common/inc/")
include_directories(${cuda_samples_include})
include_directories(${cuda_include_dirs})
set(CUDA_VERBOSE_BUILD ON)
set(CUDA_SEPARABLE_COMPILATION ON) # Allow multiple CUDA files compilation

add_executable(asyncAPI asyncAPI.cu)
set(MPIEXEC_EXECUTABLE /opt/openmpi-cuda-4.0.3/bin/mpicxx)
#set(MPI_CXX_COMPILER /usr/local/bin/mpicxx)
#set(MPI_HOME /usr/local)
find_package(MPI REQUIRED)
include_directories(${MPI_CXX_INCLUDE_DIRS})

add_executable(simpleMPI simpleMPI.cpp simpleMPI.cu)
target_link_libraries(simpleMPI ${MPI_CXX_LIBRARIES})

add_executable(cudaTensorCoreGemm cudaTensorCoreGemm.cu)
target_link_libraries(cudaTensorCoreGemm)

add_executable(bandwidthTest bandwidthTest.cu)

find_package(OpenGL REQUIRED)
add_executable(simpleGL simpleGL.cu)
target_link_libraries(simpleGL GL GLU glut)

set(VULKANSDK = /kws_space/vulkansdk_1.2.135.0/x86_64)
add_executable(simpleVulkan vulkanCUDASinewave.cu)
target_include_directories(simpleVulkan PRIVATE "${VULKANSDK}/include")
target_link_directories(simpleVulkan PRIVATE "${VULKANSDK}/lib")
target_link_libraries(simpleVulkan vulkan glfw)

add_executable(deviceQuery deviceQuery.cpp)

set_property(TARGET deviceQuery PROPERTY LINKER_LANGUAGE CUDA)
target_link_libraries(deviceQuery cuda)

add_executable(deviceQueryDrv deviceQueryDrv.cpp)
target_link_libraries(deviceQueryDrv cuda)