cmake_minimum_required(VERSION 3.0.0)
project(CCalib VERSION 1.0.0)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


# ==============
# Set build type
# ==============
set(DEFAULT_BUILD_TYPE "Release")
if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to '${DEFAULT_BUILD_TYPE}' as none was specified.")
    set(CMAKE_BUILD_TYPE "${DEFAULT_BUILD_TYPE}" CACHE STRING "Choose the type of build." FORCE)
    # Set the possible values of build type for cmake-gui
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()


# ==============================
# Link libraries via its modules
# ==============================
# Link OpenCV libraries
find_package(OpenCV REQUIRED PATHS ../OpenCV4/install/lib/cmake/opencv4)


# ==================================================================
# Add source files, libraries and define headers for these libraries
# ==================================================================
# 'random_pattern_generator' target
add_executable(random_pattern_generator samples/random_pattern_generator.cpp)
target_include_directories(random_pattern_generator
	PUBLIC  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
			$<INSTALL_INTERFACE:include>
	PRIVATE src)
target_link_libraries(random_pattern_generator ${OpenCV_LIBS})

# 'omni_calibration' target
add_executable(omni_calibration samples/omni_calibration.cpp)
target_include_directories(omni_calibration
	PUBLIC  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
			$<INSTALL_INTERFACE:include>
	PRIVATE src)
target_link_libraries(omni_calibration ${OpenCV_LIBS})

# 'omni_stereo_calibration' target
add_executable(omni_stereo_calibration samples/omni_stereo_calibration.cpp)
target_include_directories(omni_stereo_calibration
	PUBLIC  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
			$<INSTALL_INTERFACE:include>
	PRIVATE src)
target_link_libraries(omni_stereo_calibration ${OpenCV_LIBS})

# 'multi_cameras_calibration' target
add_executable(multi_cameras_calibration samples/multi_cameras_calibration.cpp)
target_include_directories(multi_cameras_calibration
	PUBLIC  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
			$<INSTALL_INTERFACE:include>
	PRIVATE src)
target_link_libraries(multi_cameras_calibration ${OpenCV_LIBS})


# ======================================
# Install targets and export config file
# ======================================
set(TARGET_LIST "random_pattern_generator" "omni_calibration" "omni_stereo_calibration" "multi_cameras_calibration")
# 'make install' to the correct locations
install(TARGETS ${TARGET_LIST} DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/bin
		EXPORT ${PROJECT_NAME}Config 
		ARCHIVE  DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/lib
		LIBRARY  DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/lib)

# Export module to the project's build directory
export( TARGETS ${TARGET_LIST}
		NAMESPACE ${PROJECT_NAME}:: 
		FILE ${PROJECT_NAME}Config.cmake)

# Export module to the project's cmake directory
export( TARGETS ${TARGET_LIST}
		NAMESPACE ${PROJECT_NAME}::
		FILE ${CMAKE_CURRENT_SOURCE_DIR}/cmake/${PROJECT_NAME}Config.cmake)

# Export module to the base-project's cmake directory
export( TARGETS ${TARGET_LIST}
		NAMESPACE ${PROJECT_NAME}:: 
		FILE ${CMAKE_SOURCE_DIR}/cmake/${PROJECT_NAME}Config.cmake)


# ======================================
# Uninstall target
# ======================================
if(NOT TARGET uninstall)
	configure_file(
		"${CMAKE_CURRENT_SOURCE_DIR}/cmake/cmake_uninstall.cmake.in"
		"${CMAKE_CURRENT_BINARY_DIR}/cmake/cmake_uninstall.cmake"
		IMMEDIATE @ONLY)

	add_custom_target(uninstall
		COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake/cmake_uninstall.cmake)
endif()