# Use the version required by SFML.
cmake_minimum_required(VERSION 3.16)

# Project file name, e.g. VS solution name.
project(openPvZ CXX)

# Recursively get source files.
file(GLOB_RECURSE SOURCES "*.c*" "*.h*")
set(RESOURCES)

# Set the assets subdirectory.
set(ASSETS_DIRECTORY "Assets" CACHE PATH "Relative path to the assets subdirectory.")
set(ASSETS_FULL_PATH "${CMAKE_CURRENT_LIST_DIR}/${ASSETS_DIRECTORY}")
message("Path to assets: ${ASSETS_FULL_PATH}")

# Recursively get assets if the folder exists.
if(EXISTS "${ASSETS_FULL_PATH}")
	set (ASSETS_EXIST ON)
	file(GLOB_RECURSE RESOURCES "${ASSETS_FULL_PATH}/*.*")
	file(GLOB_RECURSE SHADERS "${ASSETS_FULL_PATH}/*.*sh")
else()
	set (ASSETS_EXIST OFF)
    message("The Assets subfolder does not exist.")
    message("Assets will not be copied.")
endif()

# Add source files to executable.
add_executable(openPVZ ${SOURCES} ${RESOURCES})

# Create the source groups for source tree with root at PROJECT_SOURCE_DIR.
source_group(TREE ${PROJECT_SOURCE_DIR} FILES ${SOURCES} ${RESOURCES})

# Set target folder.
# Set C++20 standard.
set_target_properties(openPVZ PROPERTIES
	FOLDER openPVZ
	CXX_STANDARD 20
	CXX_STANDARD_REQUIRED ON
)

if(MSVC)
	# Enable Unicode support.
	# Disable w4201: nonstandard extension used: nameless struct/union.
	target_compile_options(openPVZ PRIVATE -DUNICODE /wd4201)

	if(PLATFORM_UNIVERSAL_WINDOWS)
        # Disable w4189: local variable is initialized but not referenced.
        # Disable w4063: case is not a valid value for switch of enum.
        # Consume the windows runtime extensions (/ZW).
		target_compile_options(openPVZ INTERFACE /wd4189 /wd4063 /ZW)
	endif()
endif()

# Enumerate engine header subdirectories.
file(GLOB_RECURSE HEADER_DIRS LIST_DIRECTORIES true RELATIVE ${CMAKE_CURRENT_LIST_DIR} "*")
list(FILTER HEADER_DIRS INCLUDE REGEX ".*(include|interface)$")

# Include headers.
target_include_directories(openPVZ PUBLIC ${HEADER_DIRS})

# Link libraries.
target_link_libraries(openPVZ
PUBLIC
	sfml-graphics
)

# Copy OpenAL DLL
if(WIN32)
    add_custom_command(
        TARGET openPVZ
        COMMENT "Copy OpenAL DLL"
        PRE_BUILD COMMAND ${CMAKE_COMMAND} -E copy ${SFML_SOURCE_DIR}/extlibs/bin/$<IF:$<EQUAL:${CMAKE_SIZEOF_VOID_P},8>,x64,x86>/openal32.dll $<TARGET_FILE_DIR:openPVZ>
        VERBATIM)
endif()
