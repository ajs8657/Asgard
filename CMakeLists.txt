# CMakeList.txt: CMake project

# Minimum CMake Version
cmake_minimum_required (VERSION 3.8)

# Project Name
project("Asgard")
set(CMAKE_CXX_STANDARD 17)

# Create a list of source files
set(SRC_DIR "src")
file(GLOB_RECURSE SRC "${SRC_DIR}/*.cpp" "${SRC_DIR}/*.h")

# Create a list of git submodules
set(GIT_SUBMODULES "Logg" "ThorEngine")

# Add and link each submodule to the project
set(VENDOR_DIR "vendor")
foreach(SUBMODULE IN LISTS GIT_SUBMODULES)

    # Include the library project directories
    add_subdirectory("${VENDOR_DIR}/${SUBMODULE}")

    # Add include directories for the build
    if(EXISTS "${VENDOR_DIR}/${SUBMODULE}/src" AND IS_DIRECTORY "${VENDOR_DIR}/${SUBMODULE}/src")
        include_directories("${VENDOR_DIR}/${SUBMODULE}/src")
    endif()
    
    # Add directories in which the linker will look for libraries
    link_directories("${VENDOR_DIR}/${SUBMODULE}")

endforeach()

# Add the source files to this project's binary
add_executable(${PROJECT_NAME} ${SRC})
set_target_properties(${PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)

# Add compile definitions to the target
target_compile_definitions(${PROJECT_NAME} PUBLIC GLFW_INCLUDE_NONE)

# Custom complile definition that defines DEBUG when building in Debug mode
target_compile_definitions(${PROJECT_NAME} PUBLIC "$<$<CONFIG:DEBUG>:DEBUG>")

# Link the submodules to the executable
foreach(SUBMODULE IN LISTS GIT_SUBMODULES)
    target_link_libraries(${PROJECT_NAME} ${SUBMODULE})
endforeach()
