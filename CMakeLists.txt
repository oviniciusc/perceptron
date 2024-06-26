# Define your standard CMake header (for example):
cmake_minimum_required(VERSION 3.5)
include(${CMAKE_CURRENT_SOURCE_DIR}/pd.build/pd.cmake)

project(pd-perceptron)

set_pd_external_path(${CMAKE_CURRENT_SOURCE_DIR}/pd-perceptron)


# Set compilation flags for ARM architecture
if(CMAKE_SYSTEM_PROCESSOR MATCHES "arm*|aarch*")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=armv8-a -mtune=cortex-a78")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=armv8-a -mtune=cortex-a78")
endif()



#╭──────────────────────────────────────╮
#│              Static GSL              │
#╰──────────────────────────────────────╯

add_subdirectory(gsl)
set_target_properties(gsl PROPERTIES LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR})
set_target_properties(gsl PROPERTIES POSITION_INDEPENDENT_CODE ON)




#╭──────────────────────────────────────╮
#│             Pd External              │
#╰──────────────────────────────────────╯
# Get all cpp files inside ${CMAKE_SOURCE_DIR}/Sources/ and add them to the project
file(GLOB EXTERNAL_SOURCES "${CMAKE_SOURCE_DIR}/*.c")
add_pd_external(pd-perceptron perceptron ${EXTERNAL_SOURCES})



target_link_libraries(pd-perceptron PRIVATE gsl)


target_include_directories(pd-perceptron PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/gsl/")


target_compile_options(pd-perceptron PRIVATE 
                "-O3" 
                "-ffast-math" 
                "-funroll-loops" 
                "-fomit-frame-pointer" 
                "-mfpmath=sse" 
                "-msse" 
                "-msse2" 
                "-msse3" 
                "-DPD"
)
