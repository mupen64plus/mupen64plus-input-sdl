cmake_minimum_required(VERSION 2.8.11)
project(mupen64plus-input-sdl)

set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(M64_APIDIR "${CMAKE_SOURCE_DIR}/../../../mupen64plus-core/src/api")

message("${CMAKE_SOURCE_DIR}/../../src/")

# State directories for modules and binaries
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/CMake)
set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/CMake/Find)

set(CMAKE_CC_FLAGS "-ffast-math -fno-strict-aliasing -fvisibility=hidden -D_GNU_SOURCE=1")

set(CMAKE_CXX_FLAGS "-pthread -fvisibility-inlines-hidden")

set(SRCS
  ${CMAKE_SOURCE_DIR}/../../src/autoconfig.c
  ${CMAKE_SOURCE_DIR}/../../src/config.c
  ${CMAKE_SOURCE_DIR}/../../src/plugin.c
  ${CMAKE_SOURCE_DIR}/../../src/sdl_key_converter.c
  )

if(WIN32)
  set(SRCS
    ${SRCS}
    ${CMAKE_SOURCE_DIR}/../../src/osal_dynamiclib_win32.c
    )
else()
  set(SRCS
    ${SRCS}
    ${CMAKE_SOURCE_DIR}/../../src/osal_dynamiclib_unix.c
    )
endif()

# Find dependencies
find_package(PNG REQUIRED)
if(NOT ZLIB_FOUND)
    include(ZLIB)
    message(FATAL_ERROR "Package zlib is required, but not found!")
endif(NOT ZLIB_FOUND)

find_package(PNG REQUIRED)
if(NOT PNG_FOUND)
    include(PNG)
    message(FATAL_ERROR "Package libpng is required, but not found!")
endif(NOT PNG_FOUND)

find_package(SDL2 REQUIRED)
if(NOT SDL2_FOUND)
    include(SDL2)
    message(FATAL_ERROR "Package SDL2 is required, but not found!")
endif(NOT SDL2_FOUND)

find_package(OpenGL REQUIRED)
if(NOT OpenGL_FOUND)
    message(FATAL_ERROR "Package OpenGL is required, but not found!")
endif(NOT OpenGL_FOUND)

find_package(Freetype REQUIRED)
if(NOT FREETYPE_FOUND)
    message(FATAL_ERROR "Package FreeType is required, but not found!")
endif(NOT FREETYPE_FOUND)

# Specify include directories
include_directories(
  ${PNG_INCLUDE_DIR}
  ${OpenGL_INCLUDE_DIR}
  ${SDL2_INCLUDE_DIR}
  ${FREETYPE_INCLUDE_DIR_ft2build}
  ${FREETYPE_INCLUDE_DIR_freetype2}
  ${M64_APIDIR}
  ${CMAKE_SOURCE_DIR}/../../src/
  )
# Create the binary
add_library(${CMAKE_PROJECT_NAME} SHARED ${SRCS})

# Link the libraries
# add_dependencies(${CMAKE_PROJECT_NAME} ${SDL2_LIBRARIES})

if(WIN32)
  target_link_libraries(${CMAKE_PROJECT_NAME}
    ${PNG_LIBRARY}
    ${OPENGL_LIBRARIES}
    ${SDL2_LIBRARY}
    ${ZLIB_LIBRARY}
    )
elseif(APPLE)
  target_link_libraries(${CMAKE_PROJECT_NAME}
    ${PNG_LIBRARY}
    ${OPENGL_LIBRARIES}
    ${SDL2_LIBRARY}
    ${ZLIB_LIBRARY}
    )
else()
  target_link_libraries(${CMAKE_PROJECT_NAME}
    ${PNG_LIBRARY}
    ${OPENGL_LIBRARIES}
    ${SDL2_LIBRARY}
    ${ZLIB_LIBRARY}
    ${FREETYPE_LIBRARIES}
    dl
    )
endif()
