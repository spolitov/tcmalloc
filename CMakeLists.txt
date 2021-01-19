cmake_minimum_required(VERSION 3.5)

# It has asm bits
enable_language (ASM)

set(LIBRARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set(SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}/tcmalloc")

set(SRCS
    ${SRC_DIR}/arena.cc
    ${SRC_DIR}/central_freelist.cc
    ${SRC_DIR}/common.cc
    ${SRC_DIR}/cpu_cache.cc
    ${SRC_DIR}/experiment.cc
    ${SRC_DIR}/experimental_4k_size_class.cc
    ${SRC_DIR}/experimental_56_size_class.cc
    ${SRC_DIR}/guarded_page_allocator.cc
    ${SRC_DIR}/huge_address_map.cc
    ${SRC_DIR}/huge_allocator.cc
    ${SRC_DIR}/huge_cache.cc
    ${SRC_DIR}/huge_page_aware_allocator.cc
    ${SRC_DIR}/internal/environment.cc
    ${SRC_DIR}/internal/logging.cc
    ${SRC_DIR}/internal/memory_stats.cc
    ${SRC_DIR}/internal/mincore.cc
    ${SRC_DIR}/internal/percpu.cc
    ${SRC_DIR}/internal/percpu_rseq_asm.S
    ${SRC_DIR}/internal/percpu_rseq_unsupported.cc
    ${SRC_DIR}/internal/proc_maps.cc
    ${SRC_DIR}/internal/util.cc
    ${SRC_DIR}/malloc_extension.cc
    ${SRC_DIR}/page_allocator.cc
    ${SRC_DIR}/page_allocator_interface.cc
    ${SRC_DIR}/page_heap.cc
    ${SRC_DIR}/pagemap.cc
    ${SRC_DIR}/parameters.cc
    ${SRC_DIR}/peak_heap_tracker.cc
    ${SRC_DIR}/runtime_size_classes.cc
    ${SRC_DIR}/sampler.cc
    ${SRC_DIR}/size_classes.cc
    ${SRC_DIR}/span.cc
    ${SRC_DIR}/stack_trace_table.cc
    ${SRC_DIR}/static_vars.cc
    ${SRC_DIR}/stats.cc
    ${SRC_DIR}/system-alloc.cc
    ${SRC_DIR}/tcmalloc.cc
    ${SRC_DIR}/thread_cache.cc
    ${SRC_DIR}/transfer_cache.cc
)

add_library(tcmalloc ${SRCS})

target_link_libraries(
    tcmalloc PUBLIC
    absl_hash
    absl_malloc_internal
    absl_stacktrace
    absl_str_format_internal
    absl_time
)

target_include_directories(tcmalloc PUBLIC ${LIBRARY_DIR})

set(DEFINITIONS)
# for RSEQ (restartable sequences) support
# define that we have __thread support
list(APPEND DEFINITIONS _GLIBCXX_HAVE_TLS)
# increase number of size classes and maximum allocation size that can be
# satisfied by front-end (i.e. lock-free).
list(APPEND DEFINITIONS TCMALLOC_4M_MAX_SIZE)

target_compile_definitions(tcmalloc PUBLIC ${DEFINITIONS})

set(OPTIONS)
list(APPEND OPTIONS -Wno-sign-compare)
list(APPEND OPTIONS -Wno-unused-function)
list(APPEND OPTIONS -Wno-unused-variable)

target_compile_options(tcmalloc PUBLIC ${OPTIONS})

set_target_properties(tcmalloc PROPERTIES PUBLIC_HEADER "tcmalloc/malloc_extension.h")

install(TARGETS tcmalloc
        PUBLIC_HEADER DESTINATION include/tcmalloc)
