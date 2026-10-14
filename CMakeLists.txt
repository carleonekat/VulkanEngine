cmake_minimum_required(VERSION 3.20)
project(VulkanEngine)

set(CMAKE_CXX_STANDARD 17)

# Find the Vulkan SDK package installed on your system
find_package(Vulkan REQUIRED)

# If you have GLFW installed locally, find it or configure its path here
# For now, we will target the main executable and link Vulkan
add_executable(VulkanEngine src/main.cpp)

# Link the Vulkan library to your executable
target_link_libraries(VulkanEngine PRIVATE Vulkan::Vulkan)
