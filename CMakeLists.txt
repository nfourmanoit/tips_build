cmake_minimum_required (VERSION 2.6 FATAL_ERROR)
cmake_policy(SET CMP0012 NEW)

# PROJECT
project (TIPS)
set(TIPS_VERSION_MAJOR 1)
set(TIPS_VERSION_MINOR 2) 
set(TIPS_VERSION_PATCH 1)

# TREE
set(SRC_DIR "${PROJECT_SOURCE_DIR}/src")
set(INC_DIR "${PROJECT_SOURCE_DIR}/include") 
set(BUILD_DIR "${PROJECT_SOURCE_DIR}/build")
set(LIB_DIR "${PROJECT_SOURCE_DIR}/lib") 
set(BIN_DIR "${PROJECT_SOURCE_DIR}/bin")
set(TEST_DIR "${PROJECT_SOURCE_DIR}/test")
set(DOC_DIR "${PROJECT_SOURCE_DIR}/doc")
if(NOT EXISTS "${BIN_DIR}")
        file(MAKE_DIRECTORY "${BIN_DIR}")
endif()
if(NOT EXISTS "${DOC_DIR}")
	file(MAKE_DIRECTORY "${DOC_DIR}")
endif()
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${LIB_DIR})
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${LIB_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR})
set(HEADER_OUTPUT_PATH ${INC_DIR})
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake/modules)
#file(GLOB cmake_modules "${CMAKE_SOURCE_DIR}/cmake/modules/*.cmake")
#foreach(module ${cmake_modules})
#	include (${module})
#endforeach(module)

# BUILD
if(NOT CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE Debug) 
        message(STATUS "Configured for \"${CMAKE_BUILD_TYPE}\" build type")
endif(NOT CMAKE_BUILD_TYPE)
set(CMAKE_COLOR_MAKEFILE ON)
set(CMAKE_VERBOSE_MAKEFILE ON)

# COMPILERS
include(CheckCCompilerFlag)
CHECK_C_COMPILER_FLAG("-W" COMPILER_SUPPORTS_WARNINGS)
CHECK_C_COMPILER_FLAG("-Wall" COMPILER_SUPPORTS_WALL)
CHECK_C_COMPILER_FLAG("-Wextra" COMPILER_SUPPORTS_WEXTRA)
if(COMPILER_SUPPORTS_WARNINGS)
	set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -W")
endif(COMPILER_SUPPORTS_WARNINGS)
if(COMPILER_SUPPORTS_WALL)
	set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
endif(COMPILER_SUPPORTS_WALL)
if(COMPILER_SUPPORTS_WEXTRA)
	set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -Wextra")
endif(COMPILER_SUPPORTS_WEXTRA)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O2")

# LIBS


# AXESIM
add_subdirectory(${SRC_DIR}/axesim ${BUILD_DIR}/axesim )



# TEST
include(CTest)
enable_testing()
if(TESTING)
	#add_subdirectory(${SRC_ROOT}/test ${TEST_ROOT})
endif(TESTING)
# add_test(NAME <name> [CONFIGURATIONS [Debug|Release|...]]
#           [WORKING_DIRECTORY dir]
#            COMMAND <command> [arg1 [arg2 ...]])

# API DOCUMENTATION
find_package(Doxygen)
if(${DOXYGEN_FOUND})
	set(doxyfile_in ${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in)
	set(doxyfile ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)
	if(EXISTS "${doxyfile_in}")
		configure_file(${doxyfile_in} ${doxyfile} @ONLY)
	message(STATUS "Doxyfile configured: '${doxyfile.in}' --> '${doxyfile}'")
else()
#message(SEND_ERROR "${doxyfile_in} does not exist!") 
set(DOC_BUILT False)
endif()
# 
# add_custom_target(doc
# COMMAND ${CMAKE_COMMAND} -E echo_append "Building API Documentation..."
# COMMAND ${DOXYGEN_EXECUTABLE} ${doxyfile}
# COMMAND ${CMAKE_COMMAND} -E echo "Done."
# WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
# VERBATIM)
# 
endif()

if (DOC_BUILT) 
message(STATUS "API documentation was generated") 
else()
message(STATUS "API documentation couldn't be generated")
endif()

# distclean target
#################
#add_custom_target(distclean COMMAND ${CMAKE_COMMAND} -P ${CMAKE_SOURCE_DIR}/distclean.cmake)
## This CMake script will delete build directories and files to bring the
## package back to it's distribution state
#
## We want to start from the top of the source dir, so if we are in build
## we want to start one directory up
#GET_FILENAME_COMPONENT(BASEDIR ${CMAKE_SOURCE_DIR} NAME)
#IF(${BASEDIR} STREQUAL "build")
#    SET(TOPDIR "${CMAKE_SOURCE_DIR}/..")
#ELSE()
#    SET(TOPDIR "${CMAKE_SOURCE_DIR}")
#ENDIF()
#
#MACRO(GET_PARENT_DIRECTORIES search_string return_list grandparents)
#    FILE(GLOB_RECURSE new_list ${search_string})
#    SET(dir_list "")
#    FOREACH(file_path ${new_list})
#        GET_FILENAME_COMPONENT(dir_path ${file_path} PATH)
#        # Remove an extra directory component to return grandparent
#        IF(${grandparents})
#            # Tack on a fake extension to trick CMake into removing a second
#            # path component
#            SET(dir_path "${dir_path}.tmp")
#            GET_FILENAME_COMPONENT(dir_path ${dir_path} PATH)
#        ENDIF(${grandparents})
#        SET(dir_list ${dir_list} ${dir_path})
#    ENDFOREACH()
#    LIST(REMOVE_DUPLICATES dir_list)
#    SET(${return_list} ${dir_list})
#ENDMACRO()
#
## Find directories and files that we will want to remove
#FILE(GLOB EGGS "${TOPDIR}/*.egg-info" "${TOPDIR}/python/*.egg-info")
#FILE(GLOB PYCACHE "${TOPDIR}/python/npspec/tests/__pycache__")
#FILE(GLOB_RECURSE PYC "${TOPDIR}/*.pyc")
#FILE(GLOB_RECURSE CBP "${TOPDIR}/*.cbp")
#FILE(GLOB PYBUILDTEMP "${TOPDIR}/build/temp*")
#FILE(GLOB PYBUILDLIB "${TOPDIR}/build/lib*")
#FILE(GLOB PYBUILDBDIST "${TOPDIR}/build/bdist*")
#FILE(GLOB_RECURSE CMAKECACHE "${TOPDIR}/*CMakeCache.txt")
#FILE(GLOB_RECURSE CMAKEINSTALL "${TOPDIR}/*cmake_install.cmake")
#FILE(GLOB_RECURSE MAKEFILE "${TOPDIR}/*Makefile")
#FILE(GLOB_RECURSE CMAKETESTFILES "${TOPDIR}/*CTestTestfile.cmake")
#FILE(GLOB TOPDIRECTORIES "${TOPDIR}/lib" 
#                         "${TOPDIR}/dist" 
#                         "${TOPDIR}/test"
#)
## CMake has trouble finding directories recursively, so locate these
## files and then save the parent directory of the files
#GET_PARENT_DIRECTORIES(Makefile.cmake CMAKEFILES 0)
#GET_PARENT_DIRECTORIES(LastTest.log CMAKETESTING 1)
#
## Place these files and directories into a list
#SET(DEL ${TOPDIRECTORIES}
#        ${EGGS}
#        ${PYCACHE}
#        ${PYC}
#        ${CBP}
#        ${PYBUILDTEMP}
#        ${PYBUILDLIB}
#        ${PYBUILDBDIST}
#        ${CMAKECACHE}
#        ${CMAKEINSTALL}
#        ${MAKEFILE}
#        ${CMAKEFILES}
#        ${CMAKETESTING}
#        ${CMAKETESTFILES}
#)
#
## If we are not in the build dir, delete that as well
#IF(NOT (${BASEDIR} STREQUAL "build"))
#    FILE(GLOB BUILD "${TOPDIR}/build")
#    SET(DEL ${DEL} ${BUILD})
#ENDIF()
#
## Loop over the directories and delete each one
#FOREACH(D ${DEL})
#    IF(EXISTS ${D})
#        FILE(REMOVE_RECURSE ${D})
#    ENDIF()
#ENDFOREACH()

# MISC
set(CONFIGURED_ONCE TRUE CACHE INTERNAL "CMake has configured at least once.")
