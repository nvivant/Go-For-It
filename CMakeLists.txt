project (go-for-it)

cmake_minimum_required (VERSION 2.8.9)
# tell cmake where its modules can be found in our project directory
list (APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
include (GNUInstallDirs)
# where we install data directory (if we have any)
set (INSTALL_PREFIX "${CMAKE_INSTALL_PREFIX}")
set (DATADIR "${INSTALL_PREFIX}/share")

set (APP_ID "com.github.jmoerman.go-for-it")

set (PKGDATADIR ${DATADIR}/${APP_ID})
set (FILE_CONF ${PROJECT_NAME}.conf)

set (EXEC_NAME ${APP_ID})
set (APP_NAME "Go For It!")
set (RELEASE_NAME "A stylish to-do list with built-in productivity timer")
set (VERSION "1.6.2")
set (VERSION_INFO "Release")
set (ICON_NAME ${APP_ID})
set (GETTEXT_PACKAGE ${APP_ID})
set (APIVERSION 0)
set (LIBNAME ${APP_ID}-${APIVERSION})
set (LIBVERSION "0")
set (SOVERSION 0)

# The path where library files should be searched for
set (LIBRARY_PATH "src")

set (PROJECT_WEBSITE "http://manuel-kehl.de/projects/go-for-it/")
set (PROJECT_REPO "https://github.com/JMoerman/Go-For-It")
set (PROJECT_DONATIONS "http://manuel-kehl.de/donations/")

#for go-for-it.pc.cmake
set (PREFIX ${CMAKE_INSTALL_PREFIX})
set (DOLLAR "$")

# find pkgconfig to make sure dependencies are installed
find_package(PkgConfig)
# check for the required dependencies
pkg_check_modules(DEPS REQUIRED 
    gtk+-3.0>=3.14.0
    glib-2.0
    libnotify
)

add_definitions(${DEPS_CFLAGS})
link_directories(${DEPS_LIBRARY_DIRS})
include_directories(${CMAKE_BINARY_DIR}/src)

pkg_check_modules (GRANITE granite>=0.3.1)
if (GRANITE_FOUND)
    message ("--   Building with granite support!")
    set (GRANITE_OPTIONS -D HAVE_GRANITE)
    set (GRANITE_PACKAGES granite)
    add_definitions(${GRANITE_CFLAGS})
    link_directories(${GRANITE_LIBRARY_DIRS})
endif ()

# disable c compiler warnings
add_definitions(-w)
add_definitions(-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")

# make sure we have vala
find_package(Vala REQUIRED)
# make sure we use vala
include(ValaVersion)
# make sure it's the desired version of vala
ensure_vala_version("0.26" MINIMUM)

option (BUILD_TESTS "Build a unit test executable." OFF)

add_subdirectory (src)
add_subdirectory (executable)
if (BUILD_TESTS)
    add_subdirectory (tests)
endif (BUILD_TESTS)
add_subdirectory (data)
add_subdirectory (style)
add_subdirectory (po)
