cmake_minimum_required(VERSION 3.24)
project(VulkanEngine)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Automatically fetch dependencies so you don't need manual installations
include(FetchContent)

# 1. Fetch SDL3 automatically from the official repository
FetchContent_Declare(
    SDL3
    GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
    GIT_TAG release-3.4.18
)
FetchContent_MakeAvailable(SDL3)

# 2. Fetch GLM math library automatically
FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm.git
    GIT_TAG 1.0.1
)
FetchContent_MakeAvailable(glm)

# 3. Fetch Volk automatically so volk.h is guaranteed to be available
FetchContent_Declare(
    volk
    GIT_REPOSITORY https://github.com/zeux/volk.git
    GIT_TAG master
)
FetchContent_MakeAvailable(volk)

# Find the Vulkan SDK components (provided by your installed Vulkan SDK)
find_package(Vulkan REQUIRED COMPONENTS volk shaderc_combined)

# Define your source and header files
add_executable(VulkanEngine
    "src/main.cpp"
    "src/application.cpp"
    "src/utils.cpp"
    "include/application.h"
    "include/utils.h"
)

# Tell the compiler where to find your header files
target_include_directories(VulkanEngine PRIVATE include)

if(MSVC)
    target_compile_options(VulkanEngine PRIVATE /std:c++latest)
endif()

# Link everything together
target_link_libraries(VulkanEngine PRIVATE
    Vulkan::Vulkan
    volk
    Vulkan::shaderc_combined
    SDL3::SDL3
    glm::glm
)
# Automatically copy SDL3.dll to the executable output directory at build time
# Automatically copy all runtime DLLs (like SDL3.dll) to the output directory at build time
add_custom_command(TARGET VulkanEngine POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        $<TARGET_RUNTIME_DLLS:VulkanEngine>
        $<TARGET_FILE_DIR:VulkanEngine>
    COMMAND_EXPAND_LISTS
)

# Automatically copy the src/shaders folder to the build output directory
add_custom_command(TARGET VulkanEngine POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory $<TARGET_FILE_DIR:VulkanEngine>/src/shaders
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_SOURCE_DIR}/src/shaders
        $<TARGET_FILE_DIR:VulkanEngine>/src/shaders
)