unset(_HDRS)
unset(_UIS)
unset(_SRCS)
unset(_RSCS)
unset(PLUGIN)

set( PLUGIN screenshotplugin )
project(${PLUGIN})
cmake_minimum_required(VERSION 3.1.0)
set( CMAKE_AUTOMOC TRUE )

set(PLUGINS_ROOT_DIR "${CMAKE_CURRENT_LIST_DIR}/../.." CACHE STRING "Plugins root path. Path where include directory placed")

if(EXISTS "${PLUGINS_ROOT_DIR}/variables.cmake")
    include("${PLUGINS_ROOT_DIR}/variables.cmake")
elseif(EXISTS "${CMAKE_CURRENT_LIST_DIR}/../../variables.cmake")
    include("${CMAKE_CURRENT_LIST_DIR}/../../variables.cmake")
else()
    message(FATAL_ERROR "No variables.cmake file found.
        Please set path to this file to PLUGINS_ROOT_DIR variable")
endif()

include_directories(
    ${CMAKE_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${PLUGINS_ROOT_DIR}/include
    ${CMAKE_CURRENT_LIST_DIR}
    ${CMAKE_CURRENT_LIST_DIR}/qxt/core
    ${CMAKE_CURRENT_LIST_DIR}/qxt/gui
)
if( UNIX AND NOT( APPLE OR CYGWIN ) )
    find_package( X11 REQUIRED )
    add_definitions( -DX11 )
    set( qxt_X11_SRCS
        qxt/gui/qxtwindowsystem_x11.cpp
    )
    set( qxt_X11_HDRS
        qxt/gui/x11info.h
    )
endif()
if( APPLE )
    set( qxt_mac_SRCS
        qxt/gui/qxtwindowsystem_mac.cpp
    )
    set( qxt_mac_HRDS
        qxt/gui/qxtwindowsystem_mac.h
    )
endif()
if( WIN32 )
    add_definitions( -DQ_WS_WIN )
    set( qxt_win_SRCS
        qxt/gui/qxtwindowsystem_win.cpp
    )
endif()
set ( _HDRS
    screenshot.h
    server.h
    editserverdlg.h
    screenshotoptions.h
    toolbar.h
    pixmapwidget.h
    options.h
    optionsdlg.h
    optionswidget.h
    screenshoticonset.h
    controller.h
    defines.h
    proxysettingsdlg.h
    qxt/core/qxtglobal.h
    qxt/gui/qxtwindowsystem.h
    ${qxt_X11_HDRS}
    ${qxt_mac_HRDS}
)
set ( _SRCS
    ${PLUGIN}.cpp
    screenshot.cpp
    server.cpp
    editserverdlg.cpp
    screenshotoptions.cpp
    toolbar.cpp
    pixmapwidget.cpp
    options.cpp
    optionsdlg.cpp
    optionswidget.cpp
    screenshoticonset.cpp
    controller.cpp
    proxysettingsdlg.cpp
    qxt/core/qxtglobal.cpp
    qxt/gui/qxtwindowsystem.cpp
    ${qxt_X11_SRCS}
    ${qxt_mac_SRCS}
    ${qxt_win_SRCS}
)
set ( _UIS
    optionswidget.ui
    editserverdlg.ui
    screenshot.ui
    screenshotoptions.ui
    optionsdlg.ui
    proxysettingsdlg.ui
)
set ( _RSCS
    ${PLUGIN}.qrc
)

find_package( Qt5 COMPONENTS Widgets Xml Network PrintSupport REQUIRED )
set(QT_DEPLIBS
    Qt5::Widgets
    Qt5::Xml
    Qt5::Network
    Qt5::PrintSupport
)
if( UNIX AND NOT( APPLE OR CYGWIN ) )
    find_package( Qt5 COMPONENTS X11Extras REQUIRED )
    set(QT_DEPLIBS
        ${QT_DEPLIBS}
        Qt5::X11Extras
    )
endif()
qt5_wrap_ui(UIS ${_UIS})
qt5_add_resources(RSCS ${_RSCS})

add_library(
    ${PLUGIN}
    MODULE
    ${_SRCS}
    ${UIS}
    ${RSCS}
)
if( UNIX AND NOT( APPLE OR CYGWIN ) )
    target_link_libraries(
        ${PLUGIN}
        ${X11_LIBRARIES}
        ${QT_DEPLIBS}
    )
    install(
        TARGETS
        ${PLUGIN}
        LIBRARY
        DESTINATION
        ${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
        RUNTIME DESTINATION
        ${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
    )
endif()
if( WIN32 )
    target_link_libraries(
        ${PLUGIN}
        ${QT_DEPLIBS}
    )
    install(
        TARGETS
        ${PLUGIN}
        LIBRARY
        DESTINATION
        ${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
        RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}
    )
endif()
