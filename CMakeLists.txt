cmake_minimum_required(VERSION 3.28)
project(Jumpy LANGUAGES CXX)

if(DEFINED BUILD_SHARED_LIBS AND NOT "${BUILD_SHARED_LIBS}" AND WIN32)
    set(SFML_USE_STATIC_STD_LIBS ON)
endif()

include(FetchContent)
FetchContent_Declare(SFML
    GIT_REPOSITORY https://github.com/SFML/SFML.git
    GIT_TAG 2.6.x
    EXCLUDE_FROM_ALL)
FetchContent_MakeAvailable(SFML)

# Find all source files
file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS ${PROJECT_SOURCE_DIR}/src/*.cpp ${PROJECT_SOURCE_DIR}/src/*.hpp)

add_executable(Jumpy ${SOURCE_FILES})
target_link_libraries(Jumpy PRIVATE sfml-graphics)
target_compile_features(Jumpy PRIVATE cxx_std_17)
target_include_directories(Jumpy PRIVATE $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>)

if(NOT ${BUILD_SHARED_LIBS})
    if(WIN32 AND MSVC)
        set_property(TARGET Jumpy PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    elseif(CMAKE_COMPILER_IS_GNUCXX)
        target_compile_options(Jumpy PRIVATE -static)
    endif()
endif()

if(WIN32)
    set_target_properties(Jumpy PROPERTIES WIN32_EXECUTABLE TRUE)
    target_link_libraries(Jumpy PRIVATE sfml-main)
endif()

install(TARGETS Jumpy DESTINATION .)
install(DIRECTORY ${PROJECT_SOURCE_DIR}/bin/res DESTINATION .)