project(ZPointCloud LANGUAGES CXX)

find_package(QT NAMES Qt5 Qt6 COMPONENTS Core REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Core Concurrent Qml 3DCore 3DInput 3DQuick 3DRender 3DQuickRender 3DLogic)

if(NOT TARGET Qt${QT_VERSION_MAJOR}::3DCore)
    message(WARNING "Qt3D not available, skipping ${PROJECT_NAME}")
    return()
endif()

file(GLOB HEADER_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/include/${PROJECT_NAME}/*.h)

file(GLOB SRC_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)

file(GLOB QQMLOBJECTLISTMODEL_SRC_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/../../3rdparty/qqmlobjectlistmodel/*.cpp)

add_library(${PROJECT_NAME} SHARED
    ${HEADER_FILES}
    ${SRC_FILES}
    zpointcloud_resources.qrc
    ${QQMLOBJECTLISTMODEL_SRC_FILES}) # FIXME

target_compile_definitions(${PROJECT_NAME} PRIVATE Z3D_${PROJECT_NAME}_LIBRARY)

target_include_directories(${PROJECT_NAME}
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/../../3rdparty/qqmlobjectlistmodel
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>)

target_link_libraries(${PROJECT_NAME}
    PRIVATE
        Qt::Qml
        Qt::3DCore
        Qt::3DInput
        Qt::3DQuick
        Qt::3DRender
        Qt::3DQuickRender
        Qt::3DLogic
    PUBLIC
        ZCore)

set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${CMAKE_PROJECT_VERSION})

install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Config
    RUNTIME
        DESTINATION bin
    LIBRARY
        DESTINATION lib
    ARCHIVE
        DESTINATION lib)

install(EXPORT ${PROJECT_NAME}Config
    FILE ${PROJECT_NAME}Config.cmake
    DESTINATION cmake)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include DESTINATION .)

# plugins
add_subdirectory(plugins)
