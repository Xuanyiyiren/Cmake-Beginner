cmake_minimum_required(VERSION 3.10)

# 声明工程名称
project(Cmake_Version3)

# 指定可执行文件目录
set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

# 将 src 目录下的源码文件保存到变量中
aux_source_directory(src SRC_LIST)

# 指定头文件路径
include_directories(include)

add_executable(main ${SRC_LIST})