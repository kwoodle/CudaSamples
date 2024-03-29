# Changes for cmake 3.18
cmake_minimum_required(VERSION 3.16)
#list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
#set(CMAKE_CUDA_HOST_COMPILER /usr/bin/g++-8)

project(CudaSamples LANGUAGES CXX CUDA)
set(CMAKE_CUDA_STANDARD 14)

set(cuda_include_dirs = ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES})
set(cuda_samples_include = "${cuda_include_dirs}/../../../samples/common/inc/")
message(STATUS "cuda_include_dirs = ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES}")
include_directories(${cuda_samples_include})
include_directories(${cuda_include_dirs})
set(CUDA_VERBOSE_BUILD ON)

add_executable(asyncAPI asyncAPI.cu)

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

#set(VULKANSDK = /mnt/ata-Linux/pop_os/vulkan_sdk/1.2.189.0/x86_64)
find_package(Vulkan REQUIRED)
add_executable(simpleVulkan vulkanCUDASinewave.cu)
target_link_libraries(simpleVulkan Vulkan::Vulkan glfw)

find_library(CUDART_LIBRARY cudart ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
message(STATUS "CUDART_LIBRARY = ${CUDART_LIBRARY}")
add_executable(deviceQuery deviceQuery.cpp)
target_link_libraries(deviceQuery ${CUDART_LIBRARY})

add_executable(deviceQueryDrv deviceQueryDrv.cpp)
target_link_libraries(deviceQueryDrv cuda)

include_directories(/opt/libcutensor/include)
add_executable(contraction contraction.cu)
target_link_directories(contraction PRIVATE /opt/libcutensor/lib/10.2)
target_link_libraries(contraction cutensor)

add_executable(reduction reduction.cu)
target_link_directories(reduction PRIVATE /opt/libcutensor/lib/10.2)
target_link_libraries(reduction cutensor)

find_library(NVJPEG_LIBRARY nvjpeg ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(NPPC_LIBRARY nppc ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(NPPIG_LIBRARY nppig ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})
find_library(CULIBOS culibos ${CMAKE_CUDA_IMPLICIT_LINK_DIRECTORIES})

add_executable(image_resize imageResize.cpp)
target_link_libraries(image_resize ${NVJPEG_LIBRARY} ${NPPC_LIBRARY} ${NPPIG_LIBRARY} ${CUDART_LIBRARY})
set_property(TARGET reduction contraction bandwidthTest simpleVulkan simpleGL
        cudaTensorCoreGemm simpleMPI asyncAPI PROPERTY CUDA_ARCHITECTURES 75)
