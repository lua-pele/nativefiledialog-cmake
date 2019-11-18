include(CheckIncludeFile)
set(SOURCES src/nfd_common.c)

macro(REQUIRE_INCLUDE_FILE path name)
    CHECK_INCLUDE_FILE(${path} ${name})
    if (NOT ${name})
        message(FATAL_ERROR "${path} not found")
    endif ()
endmacro()

# add specific implementations
if (WIN32)
    REQUIRE_INCLUDE_FILE(windows.h HAS_WINDOWS)
    list(APPEND SOURCES src/nfd_win.cpp)
elseif (APPLE)
    # REQUIRE_INCLUDE_FILE(AppKit/AppKit.h HAS_APPKIT) # Commented out by DRC to compile vulkan ui projects
    list(APPEND SOURCES src/nfd_cocoa.m)
elseif (UNIX)
    # REQUIRE_INCLUDE_FILE(gtk/gtk.h HAS_GTK) # Commented out by DRC to compile vulkan ui projects
    list(APPEND SOURCES src/nfd_gtk.c)
elseif (UNIX)
    message(FATAL_ERROR "Cannot detect your system, please report to https://github.com/aarcangeli/nativefiledialog-cmake/issues")
endif ()

add_library(nativefiledialog ${SOURCES})

if ( UNIX AND NOT APPLE )
    TARGET_LINK_LIBRARIES(nativefiledialog ${GTK3_LIBRARIES})
endif()         # Added by Dillon Ridge Computing

target_include_directories(nativefiledialog PUBLIC src/include)
