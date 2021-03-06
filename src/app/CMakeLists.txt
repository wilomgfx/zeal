# TODO: Move zeal.qrc one level up to rely on CMAKE_AUTORCC.
find_package(Qt5Core REQUIRED)
qt5_add_resources(App_RESOURCES resources/zeal.qrc)

if(APPLE)
    list(APPEND App_RESOURCES resources/zeal.icns)
elseif(WIN32)
    configure_file(versioninfo.rc.in ${CMAKE_CURRENT_BINARY_DIR}/versioninfo.rc)
    list(APPEND App_RESOURCES ${CMAKE_CURRENT_BINARY_DIR}/versioninfo.rc)
endif()

add_executable(App WIN32
    main.cpp
    ${App_RESOURCES}
)

target_link_libraries(App Core Util)

find_package(Qt5 COMPONENTS Widgets REQUIRED)
target_link_libraries(App Qt5::Widgets)

set_target_properties(App PROPERTIES
    OUTPUT_NAME ${PROJECT_OUTPUT_NAME}
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
)

if(APPLE)
    set_target_properties(App PROPERTIES
        MACOSX_BUNDLE TRUE
        MACOSX_BUNDLE_BUNDLE_NAME ${PROJECT_NAME}
        MACOSX_BUNDLE_GUI_IDENTIFIER "org.zealdocs.zeal"
        MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
        MACOSX_BUNDLE_LONG_VERSION_STRING ${PROJECT_VERSION}
        MACOSX_BUNDLE_SHORT_VERSION_STRING "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}"
        MACOSX_BUNDLE_ICON_FILE "zeal.icns"
        MACOSX_BUNDLE_COPYRIGHT ${PROJECT_COPYRIGHT}
        RESOURCE "resources/zeal.icns"
    )
elseif(UNIX)
    find_package(ECM REQUIRED NO_MODULE)
    set(CMAKE_MODULE_PATH ${ECM_KDE_MODULE_DIR})
    include(KDEInstallDirs)

    install(TARGETS App DESTINATION ${KDE_INSTALL_BINDIR})
endif()
