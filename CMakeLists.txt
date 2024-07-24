cmake_minimum_required(VERSION 3.16)
project(timelib)

# https://github.com/mongodb/mongo/blob/5981da81dd34cfc60251596c4ee796016f0e761d/src/third_party/timelib/BUILD.bazel

add_custom_command(OUTPUT parse_date.c
    COMMAND re2c -d -b ${CMAKE_CURRENT_SOURCE_DIR}/parse_date.re > parse_date.c
    DEPENDS parse_date.re timezonemap.h)

add_custom_command(OUTPUT parse_iso_intervals.c
    COMMAND re2c -d -b ${CMAKE_CURRENT_SOURCE_DIR}/parse_iso_intervals.re > parse_iso_intervals.c
    DEPENDS parse_iso_intervals.re)


add_library(timelib SHARED astro.c
    dow.c
    interval.c
    parse_posix.c
    parse_tz.c
    parse_zoneinfo.c
    timelib.c
    tm2unixtime.c
    unixtime2tm.c
    parse_date.c
    parse_iso_intervals.c)

target_include_directories(timelib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

target_compile_definitions(timelib PUBLIC -D HAVE_STDINT_H -D HAVE_GETTIMEOFDAY -DHAVE_UNISTD_H -D HAVE_DIRENT_H)
find_library(MATH_LIBRARY m)
if(MATH_LIBRARY)
    target_link_libraries(timelib PUBLIC ${MATH_LIBRARY})
endif()


add_executable(dateToParts docs/date-to-parts.c)
target_link_libraries(dateToParts PRIVATE timelib)
