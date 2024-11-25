cmake_minimum_required(VERSION 3.10.0)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake;${CMAKE_MODULE_PATH}")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

file(STRINGS "VERSION" version)
project(GlobalProtect-openconnect LANGUAGES CXX VERSION ${version})

# Set the CMAKE_INSTALL_PREFIX to /usr if not specified
if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "/usr" CACHE PATH "The default install prefix" FORCE)
endif()

message(STATUS "CMAKE_INSTALL_PREFIX was set to: ${CMAKE_INSTALL_PREFIX}")

configure_file(version.h.in version.h)

find_package(Qt5 REQUIRED COMPONENTS
    Core
    Widgets
    Network
    WebSockets
    WebEngine
    WebEngineWidgets
    DBus
)

find_package(Qt5Keychain REQUIRED)

add_subdirectory(3rdparty/qt-unix-signals)
add_subdirectory(3rdparty/inih)
add_subdirectory(GPService)
add_subdirectory(GPClient)
add_dependencies(gpclient gpservice)

# --------------------- CPack Configuration ---------------------
include(CPack)

# General package information
set(CPACK_PACKAGE_NAME "GlobalProtect-openconnect")
set(CPACK_PACKAGE_VERSION ${version})
set(CPACK_PACKAGE_CONTACT "Your Name <your.email@example.com>")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "GlobalProtect client using OpenConnect")
set(CPACK_PACKAGE_VENDOR "YourOrganization")

# DEB-specific settings
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Your Name <your.email@example.com>")
set(CPACK_DEBIAN_PACKAGE_DEPENDS "qt5-default, libqt5network5, libqt5websockets5, libqt5webengine5, libqt5webenginewidgets5, libqt5dbus5")

# RPM-specific settings
set(CPACK_RPM_PACKAGE_REQUIRES "qt5, libqt5network, libqt5websockets, libqt5webengine, libqt5webenginewidgets, libqt5dbus")
set(CPACK_RPM_PACKAGE_LICENSE "GPL")

# Generated package file name
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${CMAKE_SYSTEM_NAME}")

# Package generators
set(CPACK_GENERATOR "DEB;RPM")

include(CPack)
