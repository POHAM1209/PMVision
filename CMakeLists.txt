cmake_minimum_required(VERSION 3.5)

project(PMVision VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets)

# 添加头文件
include_directories(${CMAKE_SOURCE_DIR}/include)

# 添加源文件、资源文件及UI文件
file(GLOB INCLUDE_FILES ${CMAKE_SOURCE_DIR}/include/*.h)
file(GLOB SOURCES_FILES ${CMAKE_SOURCE_DIR}/source/*.cpp)
file(GLOB UI_FILES ${CMAKE_SOURCE_DIR}/source/*.ui)
file(GLOB QRC_FILES ${CMAKE_SOURCE_DIR}/resource/*.qrc)

# 构建
if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(${PROJECT_NAME}
        MANUAL_FINALIZATION
        ${INCLUDE_FILES} ${SOURCES_FILES} ${UI_FILES} ${QRC_FILES}
    )
# Define target properties for Android with Qt 6 as:
#    set_property(TARGET PMVision APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(PMVision SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(PMVision
            ${PROJECT_SOURCES}
        )
    endif()
endif()

# 链接QT库
target_link_libraries(PMVision PRIVATE Qt${QT_VERSION_MAJOR}::Widgets)

# 链接第三方库
set(3RDPARTY_DIR ${CMAKE_SOURCE_DIR}/3rdparty)
# include_directories(${3RDPARTY_DIR}/MVviewer/include)
# target_link_libraries(${PROJECT_NAME} PRIVATE ${3RDPARTY_DIR}/MVviewer/xx.lib)


set_target_properties(PMVision PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER my.example.com
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

install(TARGETS PMVision
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(PMVision)
endif()
