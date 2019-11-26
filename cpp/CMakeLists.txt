# Get the project name from the current directory
get_filename_component(project_name ${CMAKE_CURRENT_SOURCE_DIR} NAME)

cmake_minimum_required(VERSION 3.14.5)

project(${project_name} CXX)

set(CMAKE_CXX_STANDARD 17)

# Build executable from sources and headers
add_library(${project_name} SHARED
        src/file.cpp)

set_target_properties(${project_name} PROPERTIES
        CXX_STANDARD 17
        CXX_STANDARD_REQUIRED OFF
        CXX_EXTENSIONS OFF
)

target_include_directories(${project_name}
        PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src
        PUBLIC  ${CMAKE_CURRENT_SOURCE_DIR}/inc
)

if("${CMAKE_CXX_COMPILER_ID}" MATCHES "(GNU|Clang)")
    set_target_properties(${project_name} PROPERTIES
            COMPILE_FLAGS "-Wall -Wextra -Wpedantic -Werror"
    )
endif()

# Tell MSVC not to warn us about unchecked iterators in debug builds
if(${MSVC})
    set_target_properties(${project_name} PROPERTIES
            COMPILE_DEFINITIONS_DEBUG _SCL_SECURE_NO_WARNINGS)
endif()

# Testing
add_executable(${project_name}-test tests/file-test.cpp tests/tests-main.cpp)

set_target_properties(${project_name}-test PROPERTIES
        CXX_STANDARD 17
        CXX_STANDARD_REQUIRED OFF
        CXX_EXTENSIONS OFF
        )

target_include_directories(${project_name}-test
        PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src
        PUBLIC  ${CMAKE_CURRENT_SOURCE_DIR}/tests
)

target_link_libraries(${project_name}-test PUBLIC ${project_name})

if("${CMAKE_CXX_COMPILER_ID}" MATCHES "(GNU|Clang)")
    set_target_properties(${project_name}-test PROPERTIES
            COMPILE_FLAGS "-Wall -Wextra -Wpedantic -Werror"
            )
endif()

# Always run test when build end
add_custom_target(runTest ALL DEPENDS ${project_name}-test COMMAND ${CMAKE_CURRENT_BINARY_DIR}/${project_name}-test)
