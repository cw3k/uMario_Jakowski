cmake_minimum_required(VERSION 2.6)
project(uMario)

# Use our modified FindSDL2* modules
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${uMario_SOURCE_DIR}/cmake")
set(BIN_DIR ${uMario_SOURCE_DIR}/bin)

# Bump up warning levels appropriately for clang, gcc & msvc and build in debug mode
if (${CMAKE_CXX_COMPILER_ID} STREQUAL "GNU" OR ${CMAKE_CXX_COMPILER_ID} STREQUAL "Clang")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pedantic -std=c++11")
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS} -g")
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O2")
elseif (${CMAKE_CXX_COMPILER_ID} STREQUAL "MSVC")
	if (CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
		string(REGEX REPLACE "/W[0-4]" "/W4" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
	else()
		set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
	endif()
endif()

include(FetchContent)
set(FETCHCONTENT_QUIET FALSE)

option(SDL2MIXER_MOD OFF)
option(SDL2MIXER_WAVPACK OFF)
option(SDL2MIXER_MIDI_FLUIDSYNTH OFF)
option(SDL2MIXER_OPUS OFF)
option(SDL2MIXER_FLAC_LIBFLAC OFF)
option(SDL2MIXER_MOD_XMP OFF)

# sdl2
FetchContent_Declare(
		SDL2
		GIT_REPOSITORY  https://github.com/libsdl-org/SDL
		GIT_TAG         release-2.28.1
		GIT_SHALLOW TRUE
		GIT_PROGRESS    TRUE
)
FetchContent_MakeAvailable(SDL2)

# sdl2_image
FetchContent_Declare(
		SDL2_IMAGE
		GIT_REPOSITORY  https://github.com/libsdl-org/SDL_image
		GIT_TAG         release-2.6.3
		GIT_SHALLOW TRUE
		GIT_PROGRESS    TRUE
)

set(SDL2IMAGE_INSTALL OFF)
set(BUILD_SHARED_LIBS FALSE)

FetchContent_MakeAvailable(SDL2_IMAGE)

# sdl2_mixer
FetchContent_Declare(
		SDL2_MIXER
		GIT_REPOSITORY  https://github.com/libsdl-org/SDL_mixer.git
		GIT_TAG         release-2.6.3
		GIT_PROGRESS    TRUE
)
FetchContent_MakeAvailable(SDL2_MIXER)

set(SDL_LIBRARIES ${SDL_LIBRARIES}  SDL2::SDL2main SDL2::SDL2-static SDL2_image::SDL2_image-static SDL2_mixer::SDL2_mixer-static)

include_directories(${SDL2_INCLUDE_DIR} ${SDL2_IMAGE_INCLUDE_DIR} ${SDL2_MIXER_INCLUDE_DIR})

file(COPY uNext/files DESTINATION .)

include_directories(uNext)

file(GLOB_RECURSE uMario_SOURCES "uNext/*.cpp")
add_executable (uMario ${uMario_SOURCES})

target_link_libraries(uMario ${SDL_LIBRARIES})

install(TARGETS uMario RUNTIME DESTINATION ${BIN_DIR})
