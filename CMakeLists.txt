cmake_minimum_required(VERSION 3.5)

# It has asm bits
enable_language (ASM)

set(LIBRARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/tcmalloc")

set(SRCS
    ${LIBRARY_DIR}/arena.cc
    ${LIBRARY_DIR}/central_freelist.cc
    ${LIBRARY_DIR}/common.cc
    ${LIBRARY_DIR}/cpu_cache.cc
    ${LIBRARY_DIR}/experiment.cc
    ${LIBRARY_DIR}/experimental_4k_size_class.cc
    ${LIBRARY_DIR}/experimental_56_size_class.cc
    ${LIBRARY_DIR}/guarded_page_allocator.cc
    ${LIBRARY_DIR}/huge_address_map.cc
    ${LIBRARY_DIR}/huge_allocator.cc
    ${LIBRARY_DIR}/huge_cache.cc
    ${LIBRARY_DIR}/huge_page_aware_allocator.cc
    ${LIBRARY_DIR}/internal/environment.cc
    ${LIBRARY_DIR}/internal/logging.cc
    ${LIBRARY_DIR}/internal/memory_stats.cc
    ${LIBRARY_DIR}/internal/mincore.cc
    ${LIBRARY_DIR}/internal/percpu.cc
    ${LIBRARY_DIR}/internal/percpu_rseq_asm.S
    ${LIBRARY_DIR}/internal/percpu_rseq_unsupported.cc
    ${LIBRARY_DIR}/internal/proc_maps.cc
    ${LIBRARY_DIR}/internal/util.cc
    ${LIBRARY_DIR}/malloc_extension.cc
    ${LIBRARY_DIR}/page_allocator.cc
    ${LIBRARY_DIR}/page_allocator_interface.cc
    ${LIBRARY_DIR}/page_heap.cc
    ${LIBRARY_DIR}/pagemap.cc
    ${LIBRARY_DIR}/parameters.cc
    ${LIBRARY_DIR}/peak_heap_tracker.cc
    ${LIBRARY_DIR}/runtime_size_classes.cc
    ${LIBRARY_DIR}/sampler.cc
    ${LIBRARY_DIR}/size_classes.cc
    ${LIBRARY_DIR}/span.cc
    ${LIBRARY_DIR}/stack_trace_table.cc
    ${LIBRARY_DIR}/static_vars.cc
    ${LIBRARY_DIR}/stats.cc
    ${LIBRARY_DIR}/system-alloc.cc
    ${LIBRARY_DIR}/tcmalloc.cc
    ${LIBRARY_DIR}/thread_cache.cc
    ${LIBRARY_DIR}/transfer_cache.cc
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
