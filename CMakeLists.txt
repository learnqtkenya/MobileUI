if(NOT QT_DEFAULT_MAJOR_VERSION)
    set(QT_DEFAULT_MAJOR_VERSION 6 CACHE STRING "Qt version to use (5 or 6), defaults to 6")
endif()

# Generic dependencies
set(CORE_COMPONENTS Core Gui Qml)
find_package(Qt${QT_DEFAULT_MAJOR_VERSION} REQUIRED COMPONENTS ${CORE_COMPONENTS})

set(CORE_LIBRARIES Qt::Core Qt::Gui Qt::Qml)

# Generic sources
set(CORE_SOURCES
    MobileUI.h
    MobileUI.cpp)

# OS specific sources & dependencies
if(ANDROID)
    if(QT_DEFAULT_MAJOR_VERSION EQUAL 6)
        set(PLATFORM_SOURCES MobileUI_android_qt6.cpp)
    elseif(QT_DEFAULT_MAJOR_VERSION EQUAL 5)
        find_package(Qt${QT_DEFAULT_MAJOR_VERSION} REQUIRED COMPONENTS AndroidExtras)
        set(PLATFORM_LIBRARIES Qt::AndroidExtras)
        set(PLATFORM_SOURCES MobileUI_android_qt5.cpp)
    endif()
elseif(IOS)
    set(PLATFORM_SOURCES MobileUI_ios.mm)
    set(PLATFORM_LIBRARIES "-framework UIKit")
else()
    set(PLATFORM_SOURCES MobileUI_dummy.cpp)
endif()

################################################################################

set(MODULE_NAME MobileUI)
set(LIB_NAME ${MODULE_NAME}Lib)

qt_add_library(${LIB_NAME} STATIC)

set_target_properties(${LIB_NAME} PROPERTIES AUTOMOC ON)
target_link_libraries(${LIB_NAME} PRIVATE ${CORE_LIBRARIES} ${PLATFORM_LIBRARIES})
target_compile_definitions(${LIB_NAME} PUBLIC APP_NAME=\"${APP_NAME}\" APP_VERSION=\"${APP_VERSION}\")

list(APPEND MODULE_QML_FILES

)
list(APPEND MODULE_SOURCE_FILES
        ${CORE_SOURCES}
        ${PLATFORM_SOURCES}
)

qt_add_qml_module(${LIB_NAME}
        URI ${MODULE_NAME}
        VERSION 1.0
        RESOURCE_PREFIX /
        QML_FILES  ${MODULE_QML_FILES}
        RESOURCES  ${MODULE_RESOURCES}
        SOURCES ${MODULE_SOURCE_FILES}
        OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${MODULE_NAME}"
)
