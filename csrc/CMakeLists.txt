# Tell it to make a shared library called bitsandbytes from these source files then tell it about the include dirs and links and give it compile time macro definitions.
set(CMAKE_ENABLE_EXPORTS ON) #Not sure if this line is required, afraid to mess with it now though.
add_library (bitsandbytes SHARED
	ops.cu
	kernels.cu
	common.cpp
	cpu_ops.cpp
	pythonInterface.cpp)

include_directories(${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES} ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/../include ${CMAKE_CURRENT_SOURCE_DIR}/../dependencies/cub ${CMAKE_CURRENT_SOURCE_DIR}/../dependencies/pthread-win32/ ${CUDA_TOOLKIT_ROOT_DIR}/include)

target_link_directories(bitsandbytes PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/../dependencies/pthread-win32/build/Release/ ${CUDA_TOOLKIT_ROOT_DIR}/x64 )
add_compile_definitions(BUILD_CUDA)
IF (WIN32)
	set_property(TARGET bitsandbytes APPEND PROPERTY LINK_OPTIONS /OPT:NOREF) #Ensure it doesn't just toss out functions never called..
	target_link_libraries(bitsandbytes cudart cublas cublasLt curand cusparse pthreadVC3 )
ELSE()
	target_link_libraries(bitsandbytes cudart cublas cublasLt curand cusparse pthread ) # pthread is a guess I'm not on linux atm to test it not gonna someone else can.
ENDIF()
#Tell nvcc to compile all the shaders for all the things.
set_target_properties( bitsandbytes
                           PROPERTIES CUDA_SEPARABLE_COMPILATION ON CUDA_ARCHITECTURES all)
