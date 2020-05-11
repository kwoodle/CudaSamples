# Needs CUDAHOSTCXX=/usr/bin/cuda-g++;CUDA_LIB_PATH=/usr/local/cuda/lib64 in
# CLion Settings | Build, Execution, Deployment | CMake > Environment

cmake_minimum_required(VERSION 3.16)
project(CudaSamples LANGUAGES CXX CUDA)

set(CMAKE_CUDA_STANDARD 14)
set(CMAKE_CUDA_FLAGS ${CMAKE_CUDA_FLAGS} "-arch=sm_75")
include_directories(/usr/local/cuda/samples/common/inc)

set(CUDA_VERBOSE_BUILD ON)
set(CUDA_SEPARABLE_COMPILATION ON) # Allow multiple CUDA files compilation

add_executable(CudaSamples main.cu)

set_target_properties(
        CudaSamples
        PROPERTIES
        CUDA_SEPARABLE_COMPILATION ON)

add_executable(asyncAPI asyncAPI.cu)

find_package(MPI REQUIRED)
include_directories(${MPI_CXX_INCLUDE_DIRS})

add_executable(simpleMPI simpleMPI.cpp simpleMPI.h simpleMPI.cu)
target_link_libraries(simpleMPI ${MPI_CXX_LIBRARIES})

add_executable(cudaTensorCoreGemm cudaTensorCoreGemm.cu)
target_link_libraries(cudaTensorCoreGemm)

add_executable(bandwidthTest bandwidthTest.cu)
