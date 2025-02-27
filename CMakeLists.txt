cmake_minimum_required(VERSION 3.24)

if (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
elseif("${METAMODULE_SDK_DIR}" STREQUAL "")
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(Wildergarden VERSION 1.0.0 DESCRIPTION "Wildergarden" LANGUAGES C CXX)

# Set C++17 standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/wildergarden-vcv)
set(SRC_DIR ${SOURCE_DIR}/src)

add_library(Wildergarden STATIC)

target_compile_definitions(Wildergarden PRIVATE 
    __V1x 
    __PLUGIN
)

# Add compile options for better control over symbols
target_compile_options(Wildergarden PRIVATE
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
)

# Add linker flags for static linking
target_link_options(Wildergarden PRIVATE
    -Wl,--gc-sections
    -static-libgcc
    -static-libstdc++
)

# Add source files
target_sources(Wildergarden PRIVATE
    ${SRC_DIR}/plugin.cpp
    ${SRC_DIR}/Pascal.cpp
    ${SRC_DIR}/Dimit.cpp
)

# Add include directories
target_include_directories(Wildergarden PRIVATE 
    ${SOURCE_DIR}
    ${SRC_DIR}
)

# Create the plugin file
create_plugin(
    SOURCE_LIB      Wildergarden
    PLUGIN_NAME     Wildergarden
    PLUGIN_JSON     ${CMAKE_CURRENT_LIST_DIR}/wildergarden-vcv/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
) 