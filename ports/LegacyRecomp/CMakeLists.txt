# legacyrecomp - ReXGlue Recompiled Project
#
# SDK boilerplate lives in generated/rexglue.cmake.

cmake_minimum_required(VERSION 3.25)
project(legacyrecomp LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(generated/rexglue.cmake)

# Sources
set(LEGACYRECOMP_SOURCES
    src/main.cpp
)

if(WIN32)
    add_executable(legacyrecomp WIN32 ${LEGACYRECOMP_SOURCES})
else()
    add_executable(legacyrecomp ${LEGACYRECOMP_SOURCES})
endif()

rexglue_setup_target(legacyrecomp)

# rexauto-pch: parse the LegacyRecomp_init.h monolith once, not once per TU
# (build perf; output-neutral -- a PCH caches the AST, not emitted code).
target_precompile_headers(LegacyRecomp PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/generated/default/LegacyRecomp_init.h")

# rexauto-debug-diet: keep function symbols + line tables, drop the
# variable/type debug info that bloats the PDB and slows every relink
# (build perf; debug-info-only -- .text and codegen stay byte-identical).
if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_compile_options(LegacyRecomp PRIVATE $<$<CONFIG:RelWithDebInfo>:-gline-tables-only>)
endif()
