# Use the version required by SFML.
cmake_minimum_required(VERSION 3.16)

# Project file name, e.g. VS solution name.
project(openPvZ CXX)

# Sort the target objects into folders.
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Build SFML.
include(FetchContent)
FetchContent_Declare(SFML GIT_REPOSITORY https://github.com/SFML/SFML.git GIT_TAG 2.6.x)
FetchContent_MakeAvailable(SFML)

# Build entt.
FetchContent_Declare(entt GIT_REPOSITORY https://github.com/skypjack/entt.git GIT_TAG v3.13.x)
FetchContent_MakeAvailable(entt)

# Build the game.
add_subdirectory(openPvZ)
