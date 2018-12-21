# Build Qfplib, the ARM Cortex-M0 floating-point library

project(qfplib)

# enable verbose log. Must be after project()
set(CMAKE_VERBOSE_MAKEFILE ON)
set(ENV{VERBOSE} "1")
message("VERBOSE: $ENV{VERBOSE}")

# apply codal build settings
include("${CODAL_UTILS_LOCATION}")

# CMAKE_CURRENT_SOURCE_DIR is codal/libraries/codal-libopencm3
set(LIB_DIR "${CMAKE_CURRENT_SOURCE_DIR}")

###############################################################################
# qfplib

set(QFPLIB_DIR "${LIB_DIR}")
message("QFPLIB_DIR: ${QFPLIB_DIR}")

# find sources and headers
set(TOP_LEVEL_INCLUDE_DIRS "${QFPLIB_DIR}")
# RECURSIVE_FIND_FILE(TOP_LEVEL_SOURCE_FILES "${QFPLIB_DIR}" "*.s")
set(
    TOP_LEVEL_SOURCE_FILES
    "qfpio.s"
    "qfplib.s"
)

# create library
add_library(qfplib ${TOP_LEVEL_SOURCE_FILES})
message("TOP_LEVEL_SOURCE_FILES: ${TOP_LEVEL_SOURCE_FILES}")

###############################################################################
# global settings

# target for STM32 Blue Pill (Arm Cortex-M3), even though code is for Arm Cortex-M0 (armv6-m)
set(CMAKE_SYSTEM_PROCESSOR "armv7-m" PARENT_SCOPE)

# include all functions plus fast divide and square root 
set(
    CMAKE_ASM_FLAGS 
    "${CMAKE_ASM_FLAGS} -Dinclude_faster=1 -Dinclude_conversions=1 -Dinclude_scientific=1"
)

# include directories
include_directories(    
    ${INCLUDE_DIRS} 
    ${TOP_LEVEL_INCLUDE_DIRS}
    ${LIB_DIR}
)

# expose include directories to parent cmake
target_include_directories(qfplib PUBLIC ${TOP_LEVEL_INCLUDE_DIRS})
