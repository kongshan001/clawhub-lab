cmake_minimum_required(VERSION 3.10)
project(SkillDemo)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找 GLFW 和 epoxy
find_package(glfw3 REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(EPOXY REQUIRED epoxy)

# 添加可执行文件
add_executable(hello_triangle hello_triangle.cpp)

# 链接库
target_link_libraries(hello_triangle glfw epoxy dl)
