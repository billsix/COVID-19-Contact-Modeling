cmake_minimum_required(VERSION 3.0)

# project name, using just the C language
project(covid19contactmodeling LANGUAGES CXX)

# TODO - make the fetching and building of these static libraries
# possible on linux through an option
if(APPLE OR WIN32)
     set(BUILD_SHARED_LIBS OFF)
     if(WIN32)
         # no console on start
         set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /SUBSYSTEM:WINDOWS /ENTRY:mainCRTStartup")
     endif(WIN32)
endif()

# set SOURCE_FILES to all of the c files
FILE(GLOB SOURCE_FILES src/Circle.cpp
  src/Source.cpp
  deps/imgui/*.cpp
)


add_definitions(-DIMGUI_IMPL_OPENGL_LOADER_GL3W=1)


# definition of the covid19contactmodeling, and its dependent sources
add_executable(
    covid19contactmodeling
    ${SOURCE_FILES}
)

# add include directories for compilation
if(APPLE)
    option(GLFW_BUILD_EXAMPLES "Build the GLFW example programs" OFF)
    option(GLFW_BUILD_TESTS "Build the GLFW test programs" OFF)
    option(GLFW_INSTALL "Generate installation target" OFF)
    add_subdirectory(deps/glfw)
    include_directories(deps/glfw/include)
endif(APPLE)
if(WIN32)
    option(GLFW_BUILD_EXAMPLES "Build the GLFW example programs" OFF)
    option(GLFW_BUILD_TESTS "Build the GLFW test programs" OFF)
    option(GLFW_INSTALL "Generate installation target" OFF)
    add_subdirectory(deps/glfw)
    include_directories(deps/glfw/include)
endif(WIN32)

include_directories(deps/gl3w)

# link against the fetched libraries
if(WIN32)
    target_link_libraries(covid19contactmodeling glfw opengl32 )
elseif(APPLE)
    find_package(OpenGL REQUIRED)
    target_link_libraries(covid19contactmodeling glfw ${GLFW_LIBRARIES})
else()
    # using PkgConfig to determine how to link against
    # the system's glfw and curl
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(GLFW REQUIRED glfw3)

    include_directories(${GLFW_INCLUDE_DIR})
    target_link_libraries(covid19contactmodeling dl m pthread
        ${GLFW_LIBRARIES} ${CURL_LIBRARIES})
endif()


# Install
install(TARGETS covid19contactmodeling DESTINATION bin)
