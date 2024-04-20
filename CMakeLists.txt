cmake_minimum_required(VERSION 3.10)

project(SAIL-BENCHMARK VERSION 1.0.0
                       DESCRIPTION "Imaging Libraries Benchmarks"
                       LANGUAGES CXX)

macro(add_benchmark_skeleton)
    set(BENCH "${ARGV0}")

    add_executable(${BENCH} src/${BENCH}.cpp)
    target_link_libraries(${BENCH} benchmark::benchmark benchmark::benchmark_main)

    set_target_properties(${BENCH} PROPERTIES RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${BENCH}")
endmacro()

set(CMAKE_PREFIX_PATH
    "${PROJECT_SOURCE_DIR}/extra/B/lib/cmake/sail"
    ${CMAKE_PREFIX_PATH})

# Required pkg-config
#
set(PKG_CONFIG_EXECUTABLE "${PROJECT_SOURCE_DIR}/extra/bin/pkg-config.exe")
find_package(PkgConfig REQUIRED)

find_package(benchmark CONFIG REQUIRED)

# Path to test images
#
set(IMAGES "${PROJECT_SOURCE_DIR}/images/")
string(REPLACE "/" "\\\\" IMAGES "${IMAGES}")
add_definitions(-DIMAGES="${IMAGES}")

# Benchmark units
#
add_definitions(-DBENCHMARK_UNIT=benchmark::kMicrosecond)

#
# Benchmarks
#

# DevIL
#
pkg_check_modules(IL IMPORTED_TARGET REQUIRED DevIL)
add_benchmark_skeleton(sail-benchmark-devil)
target_link_libraries(sail-benchmark-devil PkgConfig::IL)

# SAIL
#
find_package(Sail REQUIRED)
find_package(SailManip REQUIRED)

add_benchmark_skeleton(sail-benchmark-sail)

target_link_libraries(sail-benchmark-sail SAIL::sail SAIL::sail-manip)

# WIC
#
add_benchmark_skeleton(sail-benchmark-wic)

target_link_libraries(sail-benchmark-wic Windowscodecs)
