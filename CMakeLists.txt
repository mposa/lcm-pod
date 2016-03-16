cmake_minimum_required(VERSION 2.8.0)
project(lcm)

include(ExternalProject)

# PODs out-of-source build logic
if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
	find_file(_build_dir build PATHS ${PROJECT_SOURCE_DIR} ${PROJECT_SOURCE_DIR}/.. ${PROJECT_SOURCE_DIR}/../.. ${PROJECT_SOURCE_DIR}/../../.. ${PROJECT_SOURCE_DIR}/../../../..)
	if (_build_dir)
		set(CMAKE_INSTALL_PREFIX "${_build_dir}" CACHE STRING
		"install prefix" FORCE)
	else()
		execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory ${PROJECT_SOURCE_DIR}/build)
		set(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR}/build
		CACHE STRING "install prefix" FORCE)
	endif()
endif()
message(STATUS CMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX})

ExternalProject_Add(lcm
  SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lcm/lcm-src"
  CMAKE_ARGS -G${CMAKE_GENERATOR}
  			 -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
  			 -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})

ExternalProject_Add_Step(lcm forceconfigure
	COMMAND ${CMAKE_COMMAND} -E echo "Force configure of ${proj}"
	DEPENDEES update
	DEPENDERS configure
	ALWAYS 1)

install(CODE "message(\"Nothing to do for install.\")")
