cmake_minimum_required(VERSION 3.9.0)

project(testYolov5 VERSION 1.0)

# >>> build type 
# set(CMAKE_BUILD_TYPE "Debug")				# 指定生成的版本
# set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb")
# set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
# <<<


# >>> CXX11 
set(CMAKE_CXX_STANDARD 11)				# C++ 11 编译器
SET(CMAKE_CXX_STANDARD_REQUIRED TRUE)
# <<< CXX11

set(USE_VULKAN OFF)



if (USE_VULKAN)
    # Vulkan
    message("***********" with_vulkan)
    if(WIN32)
        set(VULKAN_ROOT "D:/ncnn/VulkanSDK-1.3.216/install/Release")
        set(VULKAN_ROOT_DEBUG "D:/ncnn/VulkanSDK-1.3.216/install/Debug")
	    
        set(VULKAN_LIBRARIES
            "${VULKAN_ROOT}/Lib/vulkan-1.lib"
            "${VULKAN_ROOT}/Lib/shaderc_combined.lib"
        CACHE INTERNAL "")

        set(VULKAN_LIBRARIES_DEBUG 
            "${VULKAN_ROOT_DEBUG}/Lib/vulkan-1.lib"
            "${VULKAN_ROOT_DEBUG}/Lib/shaderc_combinedd.lib"
        CACHE INTERNAL "")
	
	    set(VULKAN_INCLUDE_PATH "${VULKAN_ROOT}/Include" CACHE INTERNAL "")

        # Ncnn
        set(NCNN_ROOT "D:/ncnn/ncnn_install/Release/ncnn_install_vulkan")
        set(NCNN_ROOT_DEBUG "D:/ncnn/ncnn_install/Debug/ncnn_install_vulkan")
        set(NCNN_LIBRARIES 
            "${NCNN_ROOT}/lib/ncnn.lib"
            "${NCNN_ROOT}/lib/SPIRV.lib"
            "${NCNN_ROOT}/lib/glslang.lib"
            "${NCNN_ROOT}/lib/GenericCodeGen.lib"
            "${NCNN_ROOT}/lib/MachineIndependent.lib"
            "${NCNN_ROOT}/lib/OGLCompiler.lib"
            "${NCNN_ROOT}/lib/OSDependent.lib"
        CACHE INTERNAL "")
        set(NCNN_LIBRARIES_DEBUG
            "${NCNN_ROOT_DEBUG}/lib/ncnnd.lib"
            "${NCNN_ROOT_DEBUG}/lib/SPIRVd.lib"
            "${NCNN_ROOT_DEBUG}/lib/glslangd.lib"
            "${NCNN_ROOT_DEBUG}/lib/GenericCodeGend.lib"
            "${NCNN_ROOT_DEBUG}/lib/MachineIndependentd.lib"
            "${NCNN_ROOT_DEBUG}/lib/OGLCompilerd.lib"
            "${NCNN_ROOT_DEBUG}/lib/OSDependentd.lib"
        CACHE INTERNAL "")
        
        set(NCNN_INCLUDE_PATH "${NCNN_ROOT}/include" CACHE INTERNAL "")
    elseif(UNIX)
    
    endif()
else()
    message("***********" no_vulkan)
    if (WIN32)
        # ncnn
        set(NCNN_ROOT "D:/ncnn/ncnn_install/Release/ncnn_install_no_vulkan")
        set(NCNN_ROOT_DEBUG "D:/ncnn/ncnn_install/Debug/ncnn_install_no_vulkan")
        set(NCNN_LIBRARIES 
            "${NCNN_ROOT}/lib/ncnn.lib"
        CACHE INTERNAL "")
        set(NCNN_LIBRARIES_DEBUG
            "${NCNN_ROOT_DEBUG}/lib/ncnnd.lib"
        CACHE INTERNAL "")

        set(NCNN_INCLUDE_PATH "${NCNN_ROOT_DEBUG}/include" CACHE INTERNAL "")

    elseif(UNIX)
        # ncnn
        set(NCNN_ROOT "/root/package/ncnnCompiler/ncnn/build/install")
        set(NCNN_LIBRARIES 
            "${NCNN_ROOT}/lib/libncnn.a"
        CACHE INTERNAL "")
        set(NCNN_LIBRARIES_DEBUG ${NCNN_LIBRARIES} CACHE INTERNAL "")

        set(NCNN_INCLUDE_PATH "${NCNN_ROOT}/include" CACHE INTERNAL "")
    
    endif()
        
endif()

# OpenCV
if(WIN32)
    set(OPENCV_ROOT "D:/opencv-4.5.4/Release")
    set(OPENCV_ROOT_DEBUG "D:/opencv-4.5.4/Debug")
	
    set(OPENCV_LIBRARIES 
        "${OPENCV_ROOT}/lib/x64/opencv_world454.lib"
    CACHE INTERNAL "")
    set(OPENCV_LIBRARIES_DEBUG
        "${OPENCV_ROOT_DEBUG}/lib/x64/opencv_world454d.lib"
    CACHE INTERNAL "")
	
    set(OPENCV_RUNTIME_LIBRARIES
        "${OPENCV_ROOT}/bin/opencv_world454.dll"
    CACHE INTERNAL "")
    set(OPENCV_RUNTIME_LIBRARIES_DEBUG
        "${OPENCV_ROOT_DEBUG}/bin/opencv_world454d.dll"
    CACHE INTERNAL "")
	set(OPENCV_INCLUDE_PATH "${OPENCV_ROOT}/include" CACHE INTERNAL "")
elseif(UNIX)
    set(OPENCV_ROOT "/home/opencv-4.5.4/Release")
    set(OPENCV_LIBRARIES 
        "${OPENCV_ROOT}/lib/Linux64/libopencv_core.so"
        "${OPENCV_ROOT}/lib/Linux64/libopencv_videoio.so"
        "${OPENCV_ROOT}/lib/Linux64/libopencv_highgui.so"
        "${OPENCV_ROOT}/lib/Linux64/libopencv_imgcodecs.so"
        "${OPENCV_ROOT}/lib/Linux64/libopencv_imgproc.so"
	    "${OPENCV_ROOT}/lib/Linux64/libopencv_dnn.so"
        "${OPENCV_ROOT}/lib/Linux64/libopencv_flann.so"
    CACHE INTERNAL "")
    set(OPENCV_LIBRARIES_DEBUG ${OPENCV_LIBRARIES} CACHE INTERNAL "")
    file(GLOB TMP_SO_FILES "${OPENCV_ROOT}/lib/Linux64/*.so*")
    set(OPENCV_RUNTIME_LIBRARIES ${TMP_SO_FILES} CACHE INTERNAL "")
    set(OPENCV_RUNTIME_LIBRARIES_DEBUG ${TMP_SO_FILES} CACHE INTERNAL "")
	set(OPENCV_INCLUDE_PATH "${OPENCV_ROOT}/include" CACHE INTERNAL "")
endif()

add_executable(${PROJECT_NAME} yolov5.cpp)
target_include_directories(${PROJECT_NAME}  
PRIVATE 
	${NCNN_INCLUDE_PATH}
    ${OPENCV_INCLUDE_PATH}
)

if (USE_VULKAN)
    target_include_directories(${PROJECT_NAME}  
    PRIVATE 
        ${NCNN_INCLUDE_PATH}
        ${OPENCV_INCLUDE_PATH}
	    ${VULKAN_INCLUDE_PATH}
    )
    #message("vulkan*" ${VULKAN_INCLUDE_PATH})

else()
    target_include_directories(${PROJECT_NAME}  
    PRIVATE 
	    ${NCNN_INCLUDE_PATH}
        ${OPENCV_INCLUDE_PATH}
    )
endif()


message("vulkan " ${VULKAN_INCLUDE_PATH})
message("vulkan debug: " ${VULKAN_LIBRARIES_DEBUG})
message("vulkan release: " ${VULKAN_LIBRARIES})
message("ncnn: " ${NCNN_INCLUDE_PATH})
message("ncnn debug: " ${NCNN_LIBRARIES_DEBUG})
message("ncnn release: " ${NCNN_LIBRARIES})


if (USE_VULKAN)
    
    target_link_libraries(${PROJECT_NAME} 
    PUBLIC
        # debug ${NCNN_LIBRARIES_DEBUG}
	    # optimized ${NCNN_LIBRARIES}
        "$<IF:$<CONFIG:debug>,${NCNN_LIBRARIES_DEBUG},${NCNN_LIBRARIES}>"
        debug ${OPENCV_LIBRARIES_DEBUG}
	    optimized ${OPENCV_LIBRARIES}
        # debug ${VULKAN_LIBRARIES_DEBUG}
        # optimized ${VULKAN_LIBRARIES}
        "$<IF:$<CONFIG:debug>,${VULKAN_LIBRARIES_DEBUG},${VULKAN_LIBRARIES}>"
    ) 
    

else()
    target_link_libraries(${PROJECT_NAME} 
    PUBLIC
	    debug ${NCNN_LIBRARIES_DEBUG}
	    optimized ${NCNN_LIBRARIES}
        debug ${OPENCV_LIBRARIES_DEBUG}
	    optimized ${OPENCV_LIBRARIES}
    ) 
endif()

if(WIN32)

set(DLL_FILES
    ${OPENCV_RUNTIME_LIBRARIES}
)

set(DLL_FILES_DEBUG 
    ${OPENCV_RUNTIME_LIBRARIES_DEBUG}
)

add_custom_command(
TARGET ${PROJECT_NAME}
COMMAND ${CMAKE_COMMAND} -E copy_if_different "$<IF:$<CONFIG:Debug>,${DLL_FILES_DEBUG},${DLL_FILES}>" $<TARGET_FILE_DIR:${PROJECT_NAME}>
COMMAND_EXPAND_LISTS
)
endif()



    


