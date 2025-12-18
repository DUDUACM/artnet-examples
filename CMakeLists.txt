cmake_minimum_required(VERSION 3.15)
project(artnet-examples VERSION 0.3.13 LANGUAGES C)

# Set C standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Compiler flags
if(MSVC)
    add_compile_options(/W4)
else()
    add_compile_options(-Wall -Werror)
endif()

# Detect operating system
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    set(OS_LINUX TRUE)
    add_definitions(-DOS_LINUX)
    message(STATUS "Operating system: Linux")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    set(OS_DARWIN TRUE)
    add_definitions(-DOS_DARWIN)
    message(STATUS "Operating system: Darwin/MacOS X")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows")
    set(OS_WINDOWS TRUE)
    add_definitions(-DOS_WINDOWS)
    message(STATUS "Operating system: Windows")
else()
    message(FATAL_ERROR "Unsupported operating system: ${CMAKE_SYSTEM_NAME}")
endif()

# Options
option(BUILD_NCURSES_PROGS "Build ncurses-based programs" ON)

# Find libartnet library
find_package(PkgConfig)
if(PkgConfig_FOUND)
    pkg_check_modules(LIBARTNET libartnet>=1.1.0)
endif()

if(NOT LIBARTNET_FOUND)
    include(FetchContent)
    message(STATUS "Fetching libartnet from GitHub...")
    FetchContent_Declare(
        libartnet
        GIT_REPOSITORY https://github.com/OpenLightingProject/libartnet.git
        GIT_TAG master
        GIT_SHALLOW TRUE
    )
    FetchContent_MakeAvailable(libartnet)
    
    # Set variables for the fetched libartnet
    # Assuming libartnet creates a target named 'artnet'
    if(TARGET artnet)
        set(LIBARTNET_LIBRARIES artnet)
        get_target_property(LIBARTNET_INCLUDE_DIRS artnet INTERFACE_INCLUDE_DIRECTORIES)
        message(STATUS "Using FetchContent libartnet target: artnet")
    elseif(TARGET libartnet)
        set(LIBARTNET_LIBRARIES libartnet)
        get_target_property(LIBARTNET_INCLUDE_DIRS libartnet INTERFACE_INCLUDE_DIRECTORIES)
        message(STATUS "Using FetchContent libartnet target: libartnet")
    else()
        # Fallback: assume standard build structure
        set(LIBARTNET_INCLUDE_DIRS ${libartnet_SOURCE_DIR}/include)
        set(LIBARTNET_LIBRARIES artnet)
        message(STATUS "Using FetchContent libartnet with manual paths")
    endif()
else()
    message(STATUS "Found libartnet via pkg-config: ${LIBARTNET_LIBRARIES}")
endif()

# Check for ncurses
if(BUILD_NCURSES_PROGS)
    if(UNIX)
        find_library(CURSES_LIBRARIES NAMES ncurses curses)
        find_path(CURSES_INCLUDE_DIRS NAMES ncurses.h curses.h)
        if(CURSES_LIBRARIES)
            set(HAVE_NCURSES TRUE)
            message(STATUS "Found ncurses: ${CURSES_LIBRARIES}")
        else()
            set(HAVE_NCURSES FALSE)
            message(STATUS "ncurses not found, skipping ncurses-based programs")
        endif()
    else()
        set(HAVE_NCURSES FALSE)
        message(STATUS "ncurses programs not available on this platform")
    endif()
else()
    set(HAVE_NCURSES FALSE)
endif()

# Find pthread for artnet-usb on Linux
if(OS_LINUX)
    set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
    set(THREADS_PREFER_PTHREAD_FLAG TRUE)
    find_package(Threads REQUIRED)
endif()

# Include subdirectory
add_subdirectory(src)

# Installation
install(FILES README AUTHORS COPYING ChangeLog NEWS
    DESTINATION share/doc/artnet-examples
)
