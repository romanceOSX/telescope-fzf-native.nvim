cmake_minimum_required(VERSION 3.10)
project(fzf LANGUAGES C)

add_library(${PROJECT_NAME} SHARED "src/fzf.c")

target_include_directories(${PROJECT_NAME} PUBLIC
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/src>)
target_compile_options(${PROJECT_NAME}
  PRIVATE
    $<$<C_COMPILER_ID:MSVC>:/W4>
    $<$<NOT:$<C_COMPILER_ID:MSVC>>:-Wall>)
target_compile_definitions(${PROJECT_NAME}
  PRIVATE
    $<$<PLATFORM_ID:Windows>:_CRT_NONSTDC_NO_DEPRECATE>
    $<$<PLATFORM_ID:Windows>:_CRT_SECURE_NO_DEPRECATE>
    $<$<PLATFORM_ID:Windows>:_CRT_SECURE_NO_WARNINGS>)

set_target_properties(${PROJECT_NAME} PROPERTIES
    WINDOWS_EXPORT_ALL_SYMBOLS ON
    C_STANDARD 99
    PREFIX lib)

# This cannot be a generator expression in this version of CMake
if (NOT (MSVC OR MSYS OR MINGW OR WIN32))
  set_property(TARGET ${PROJECT_NAME} PROPERTY SUFFIX .so)
endif()

# output the binary in the build folder in all supported buildsystems

# for non-multi-config buildsystems (make)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# config for multi-config buildsystems
foreach(OUTPUTCONFIG ${CMAKE_CONFIGURATION_TYPES})
    string(TOUPPER ${OUTPUTCONFIG} OUTPUTCONFIG)
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_BINARY_DIR})
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_BINARY_DIR})
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_${OUTPUTCONFIG} ${CMAKE_BINARY_DIR})
endforeach()

install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_BINARY_DIR})

