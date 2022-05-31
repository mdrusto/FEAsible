cmake_minimum_required(VERSION 3.10)

set(CMAKE_C_COMPILER "D:/MinGW/bin/gcc.exe")
set(CMAKE_CXX_COMPILER "D:/MinGW/bin/g++.exe")

project(Feasible LANGUAGES C CXX)

set(GLOBAL PROPERTY USE_FOLDERS ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Git submodules
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
# Update submodules as needed
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
        endif()
    endif()
endif()

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/extern/imgui/CMakeLists.txt")
    message(FATAL_ERROR "The submodules were not downloaded! GIT_SUBMODULE was turned off or failed. Please update submodules and try again.")
endif()

# Dependencies
add_subdirectory(extern/glfw)
add_subdirectory(extern/imgui)

add_executable(
	${PROJECT_NAME}
	src/main.cpp
)

target_include_directories(
	${PROJECT_NAME}
	PRIVATE
	"$CMAKE_CURRENT_LIST_DIR}/src"
)

target_link_libraries(
	${PROJECT_NAME}
	PRIVATE
	imgui
)