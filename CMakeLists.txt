project(my_project)
cmake_minimum_required(VERSION 3.16)

set(RAYLIB_VERSION 4.5.0)
find_package(raylib ${RAYLIB_VERSION} QUIET)
if (NOT raylib_FOUND) # If there's none, fetch and build raylib
  include(FetchContent)
  FetchContent_Declare(
    raylib
    DOWNLOAD_EXTRACT_TIMESTAMP OFF
    URL https://github.com/raysan5/raylib/archive/refs/tags/${RAYLIB_VERSION}.tar.gz
  )
  FetchContent_GetProperties(raylib)
  if (NOT raylib_POPULATED) # Have we downloaded raylib yet?
    set(FETCHCONTENT_QUIET NO)
    FetchContent_Populate(raylib)
    set(BUILD_EXAMPLES OFF CACHE BOOL "" FORCE) # don't build the supplied examples
    add_subdirectory(${raylib_SOURCE_DIR} ${raylib_BINARY_DIR})
  endif()
endif()

add_executable(myapp main.c)  # Provide the target name and source file(s) here

# Enable static linking for raylib
set_target_properties(raylib PROPERTIES LINK_SEARCH_START_STATIC 1)
set_target_properties(raylib PROPERTIES LINK_SEARCH_END_STATIC 1)

# Manually link the required libraries statically
target_link_libraries(myapp
  -Wl,--whole-archive
  raylib
  -Wl,--no-whole-archive
  -lm
  -lpthread
  -ldl
  -lrt
  -lX11
)

# Link the OpenGL library dynamically
target_link_libraries(myapp -lGL)
