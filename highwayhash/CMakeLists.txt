add_library(compiler_specific INTERFACE)
target_sources(compiler_specific
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/compiler_specific.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/compiler_specific.h>)


add_library(arch_specific STATIC)
target_sources(arch_specific
    PUBLIC
    $<INSTALL_INTERFACE:include/highwayhash/arch_specific.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/arch_specific.h>
    PRIVATE arch_specific.cc)
target_link_libraries(arch_specific
    PUBLIC compiler_specific)


add_library(instruction_sets STATIC)
target_sources(instruction_sets
    PUBLIC
    $<INSTALL_INTERFACE:include/highwayhash/instruction_sets.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/instruction_sets.h>
    PRIVATE instruction_sets.cc)
target_link_libraries(instruction_sets
    PUBLIC compiler_specific arch_specific)


add_library(hh_types INTERFACE)
target_sources(hh_types
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/hh_types.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_types.h>)
target_link_libraries(hh_types INTERFACE instruction_sets)


add_library(iaca INTERFACE)
target_sources(iaca
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/iaca.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/iaca.h>)
target_link_libraries(hh_types INTERFACE compiler_specific)


add_library(endianess INTERFACE)
target_sources(endianess
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/endianess.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/endianess.h>)


add_library(load3 INTERFACE)
target_sources(load3
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/load3.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/load3.h>)
target_link_libraries(load3
    INTERFACE arch_specific compiler_specific endianess)


add_library(vector128 INTERFACE)
target_sources(vector128
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/vector128.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/vector128.h>)
target_link_libraries(vector128
    INTERFACE arch_specific compiler_specific)


add_library(vector256 INTERFACE)
target_sources(vector256
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/vector256.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/vector256.h>)
target_link_libraries(vector256
    INTERFACE arch_specific compiler_specific)


add_library(scalar INTERFACE)
target_sources(scalar
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/scalar.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/scalar.h>)
target_link_libraries(scalar
    INTERFACE arch_specific compiler_specific)


add_library(hh_avx2 STATIC)
target_sources(hh_avx2
    PUBLIC
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash_target.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash_target.h>
    $<INSTALL_INTERFACE:include/highwayhash/hh_avx2.h>
    INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_avx2.h>
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash.h>
    $<INSTALL_INTERFACE:include/highwayhash/hh_buffer.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_buffer.h>
    PRIVATE hh_avx2.cc)
target_link_libraries(hh_avx2
    PUBLIC arch_specific compiler_specific hh_types iaca load3 vector128 vector256)
target_compile_definitions(hh_avx2
    # Do not turn on `-mavx2` by defining -DHH_DISABLE_TARGET_SPECIFIC
    PUBLIC -DHH_DISABLE_TARGET_SPECIFIC)


add_library(hh_portable STATIC)
target_sources(hh_portable
    PUBLIC
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash_target.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash_target.h>
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/hh_portable.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_portable.h>
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash.h>
    $<INSTALL_INTERFACE:include/highwayhash/hh_buffer.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_buffer.h>
    PRIVATE hh_portable.cc)
target_link_libraries(hh_portable
    PUBLIC arch_specific compiler_specific hh_types iaca load3 scalar)


add_library(hh_sse41 STATIC)
target_sources(hh_sse41
    PUBLIC
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash_target.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash_target.h>
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/hh_sse41.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_sse41.h>
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash.h>
    $<INSTALL_INTERFACE:include/highwayhash/hh_buffer.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/hh_buffer.h>
    PRIVATE hh_sse41.cc)
target_link_libraries(hh_sse41
    PUBLIC arch_specific compiler_specific hh_types iaca load3 vector128)
target_compile_definitions(hh_sse41
    # Do not turn on `-mavx2` by defining -DHH_DISABLE_TARGET_SPECIFIC
    PUBLIC -DHH_DISABLE_TARGET_SPECIFIC)


add_library(highwayhash_dynamic INTERFACE)
target_sources(highwayhash_dynamic
    INTERFACE
    $<INSTALL_INTERFACE:include/highwayhash/highwayhash_target.h>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/highwayhash_target.h>)
target_link_libraries(highwayhash_dynamic
    INTERFACE arch_specific compiler_specific hh_avx2 hh_portable hh_sse41 hh_types)
