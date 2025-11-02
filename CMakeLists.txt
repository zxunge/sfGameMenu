cmake_minimum_required(VERSION 3.20)
project(gamemenu)

set(VERSION_MAJOR 2)
set(VERSION_MINOR 0)
set(VERSION_PATCH 1)

set(CMAKE_CXX_STANDARD 20)

if (NOT DEFINED LINK_TYPE)
    message("Library link type not specified, using default - STATIC")
    set(LINK_TYPE STATIC)
    option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
endif()

find_package(SFML 3 REQUIRED COMPONENTS Graphics)

set(SOURCES
    "public/game_menu/game_menu.h"
    "src/game_menu_impl.cpp"
    "src/game_menu_impl.h"
)

add_library(gamemenu ${LINK_TYPE} ${SOURCES})
target_link_libraries(gamemenu PUBLIC SFML::Graphics)
target_include_directories(
    gamemenu
    PUBLIC
    "public"
    "include"
)

set(GMENU_HEADERS
    "public/game_menu/game_menu.h"
)

set_target_properties(gamemenu PROPERTIES
    FRAMEWORK TRUE
    FRAMEWORK_VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}
    MACOSX_FRAMEWORK_IDENTIFIER in.sidhin.gamemenu
    MACOSX_FRAMEWORK_SHORT_VERSION_STRING ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}
    MACOSX_FRAMEWORK_BUNDLE_VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}
    PUBLIC_HEADER "${GMENU_HEADERS}")

option(BUILD_WITH_EXAMPLE "Build attached example" OFF)

if(BUILD_WITH_EXAMPLE)
    add_executable(example
        "examples/sample_menu.cpp"
    )
    target_link_libraries(example PRIVATE gamemenu)
    add_custom_target(copy_example_font
        COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/examples/sansation.ttf ${CMAKE_BINARY_DIR}
    )
    add_dependencies(example copy_example_font)
endif()

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
