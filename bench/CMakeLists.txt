# sources
set(SOURCES bench.c)


# targets
add_executable(bench ${SOURCES})
if(UNIX AND NOT APPLE)
  # cmake is complaining about LINK_PRIVATE in original PR
  # and removing it does not seem to hurt, so be it.
  # target_link_libraries(bench LINK_PRIVATE rt)
  target_link_libraries(bench rt)
endif(UNIX AND NOT APPLE)
target_link_libraries(bench blosc_shared)

# have to copy blosc dlls for Visual Studio
if(MSVC)
    add_custom_command(
        TARGET      bench
        POST_BUILD
        COMMAND     ${CMAKE_COMMAND}
        ARGS        -E copy_if_different
                    "${PROJECT_BINARY_DIR}/blosc/\$\(Configuration\)/blosc.dll"
                    "${CMAKE_CURRENT_BINARY_DIR}/\$\(Configuration\)/blosc.dll")
endif(MSVC)


# tests
if(BUILD_TESTS)

    option(TEST_INCLUDE_BENCH_SINGLE_1 "Include bench single (1 thread) in the tests" ON)
    if(TEST_INCLUDE_BENCH_SINGLE_1)
        add_test(test_blosclz_1 bench blosclz single 1)
        if (HAVE_LZ4)
            add_test(test_lz4_1     bench lz4     single 1)
            add_test(test_lz4hc_1   bench lz4hc   single 1)
        endif (HAVE_LZ4)
        if (HAVE_SNAPPY)
            add_test(test_snappy_1  bench snappy  single 1)
        endif (HAVE_SNAPPY)
        if (HAVE_ZLIB)
            add_test(test_zlib_1    bench zlib    single 1)
        endif (HAVE_ZLIB)
    endif(TEST_INCLUDE_BENCH_SINGLE_1)

    option(TEST_INCLUDE_BENCH_SINGLE_N "Include bench single (multithread) in the tests" ON)
    if(TEST_INCLUDE_BENCH_SINGLE_N)
        add_test(test_blosclz_n bench blosclz single)
        if (HAVE_LZ4)
            add_test(test_lz4_n     bench lz4     single)
            add_test(test_lz4hc_n   bench lz4hc   single)
        endif (HAVE_LZ4)
        if (HAVE_SNAPPY)
            add_test(test_snappy_n  bench snappy  single)
        endif (HAVE_SNAPPY)
        if (HAVE_ZLIB)
            add_test(test_zlib_n    bench zlib    single)
        endif (HAVE_ZLIB)
    endif(TEST_INCLUDE_BENCH_SINGLE_N)

    option(TEST_INCLUDE_BENCH_SUITE "Include bench suite in the tests" OFF)
    if(TEST_INCLUDE_BENCH_SUITE)
        add_test(test_blosclz bench blosclz suite)
        if (HAVE_LZ4)
            add_test(test_lz4     bench lz4     suite)
            add_test(test_lz4hc   bench lz4hc   suite)
        endif (HAVE_LZ4)
        if (HAVE_SNAPPY)
            add_test(test_snappy  bench snappy  suite)
        endif (HAVE_SNAPPY)
        if (HAVE_ZLIB)
            add_test(test_zlib    bench zlib    suite)
        endif (HAVE_ZLIB)
    endif(TEST_INCLUDE_BENCH_SUITE)

    option(TEST_INCLUDE_BENCH_HARDSUITE "Include bench hardsuite in the tests" OFF)
    if(TEST_INCLUDE_BENCH_HARDSUITE)
        add_test(test_hardsuite blosc blosclz hardsuite)
    endif(TEST_INCLUDE_BENCH_HARDSUITE)

    option(TEST_INCLUDE_BENCH_EXTREMESUITE "Include bench extremesuite in the tests" OFF)
    if(TEST_INCLUDE_BENCH_EXTREMESUITE)
        add_test(test_extremesuite bench blosclz extremesuite)
    endif(TEST_INCLUDE_BENCH_EXTREMESUITE)

    option(TEST_INCLUDE_BENCH_DEBUGSUITE "Include bench debugsuite in the tests" OFF)
    if(TEST_INCLUDE_BENCH_DEBUGSUITE)
        add_test(test_debugsuite bench debugsuite)
    endif(TEST_INCLUDE_BENCH_DEBUGSUITE)
endif(BUILD_TESTS)
