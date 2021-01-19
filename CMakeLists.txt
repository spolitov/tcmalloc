# It has asm bits
enable_language (ASM)

set(LIBRARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}")

set(SRCS
    ${LIBRARY_DIR}/tcmalloc/arena.cc
    ${LIBRARY_DIR}/tcmalloc/central_freelist.cc
    ${LIBRARY_DIR}/tcmalloc/common.cc
    ${LIBRARY_DIR}/tcmalloc/cpu_cache.cc
    ${LIBRARY_DIR}/tcmalloc/experiment.cc
    ${LIBRARY_DIR}/tcmalloc/experimental_4k_size_class.cc
    ${LIBRARY_DIR}/tcmalloc/experimental_56_size_class.cc
    ${LIBRARY_DIR}/tcmalloc/guarded_page_allocator.cc
    ${LIBRARY_DIR}/tcmalloc/huge_address_map.cc
    ${LIBRARY_DIR}/tcmalloc/huge_allocator.cc
    ${LIBRARY_DIR}/tcmalloc/huge_cache.cc
    ${LIBRARY_DIR}/tcmalloc/huge_page_aware_allocator.cc
    ${LIBRARY_DIR}/tcmalloc/internal/environment.cc
    ${LIBRARY_DIR}/tcmalloc/internal/logging.cc
    ${LIBRARY_DIR}/tcmalloc/internal/memory_stats.cc
    ${LIBRARY_DIR}/tcmalloc/internal/mincore.cc
    ${LIBRARY_DIR}/tcmalloc/internal/percpu.cc
    ${LIBRARY_DIR}/tcmalloc/internal/percpu_rseq_asm.S
    ${LIBRARY_DIR}/tcmalloc/internal/percpu_rseq_unsupported.cc
    ${LIBRARY_DIR}/tcmalloc/internal/proc_maps.cc
    ${LIBRARY_DIR}/tcmalloc/internal/util.cc
    ${LIBRARY_DIR}/tcmalloc/malloc_extension.cc
    ${LIBRARY_DIR}/tcmalloc/page_allocator.cc
    ${LIBRARY_DIR}/tcmalloc/page_allocator_interface.cc
    ${LIBRARY_DIR}/tcmalloc/page_heap.cc
    ${LIBRARY_DIR}/tcmalloc/pagemap.cc
    ${LIBRARY_DIR}/tcmalloc/parameters.cc
    ${LIBRARY_DIR}/tcmalloc/peak_heap_tracker.cc
    ${LIBRARY_DIR}/tcmalloc/runtime_size_classes.cc
    ${LIBRARY_DIR}/tcmalloc/sampler.cc
    ${LIBRARY_DIR}/tcmalloc/size_classes.cc
    ${LIBRARY_DIR}/tcmalloc/span.cc
    ${LIBRARY_DIR}/tcmalloc/stack_trace_table.cc
    ${LIBRARY_DIR}/tcmalloc/static_vars.cc
    ${LIBRARY_DIR}/tcmalloc/stats.cc
    ${LIBRARY_DIR}/tcmalloc/system-alloc.cc
    ${LIBRARY_DIR}/tcmalloc/tcmalloc.cc
    ${LIBRARY_DIR}/tcmalloc/thread_cache.cc
    ${LIBRARY_DIR}/tcmalloc/transfer_cache.cc
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
