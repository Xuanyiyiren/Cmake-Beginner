cmake_minimum_required(VERSION 3.10)

# 设置目标名称
set(TARGET_NAME main)

# 声明工程名称
project(${TARGET_NAME})

# 讲当前目录./ 下的所有源码文件保存到 SRC_LIST 变量中
aux_source_directory(. SRC_LIST)

# 声明生成的目标文件和依赖代码
add_executable(${TARGET_NAME} ${SRC_LIST})