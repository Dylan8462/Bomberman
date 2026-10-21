cmake_minimum_required(VERSION 3.16)
project(Bomberman LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

include(FetchContent)
FetchContent_Declare(SFML
    GIT_REPOSITORY https://github.com/SFML/SFML.git
    GIT_TAG 3.0.0)
set(SFML_BUILD_NETWORK OFF)
FetchContent_MakeAvailable(SFML)

file(GLOB SOURCES CONFIGURE_DEPENDS Bomberman/*.cpp)
add_executable(Bomberman ${SOURCES})
target_link_libraries(Bomberman PRIVATE
    sfml-graphics sfml-window sfml-system sfml-audio)

# Copy game data next to the exe after each build
foreach(dir Audio Textures)
    add_custom_command(TARGET Bomberman POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
                ${CMAKE_SOURCE_DIR}/Bomberman/${dir}
                $<TARGET_FILE_DIR:Bomberman>/${dir})
endforeach()

# Savedata is copied only once, so rebuilds don't reset the high score
if(NOT EXISTS ${CMAKE_BINARY_DIR}/bin/Savedata/highscore.txt)
    file(COPY ${CMAKE_SOURCE_DIR}/Bomberman/Savedata DESTINATION ${CMAKE_BINARY_DIR}/bin)
endif()
