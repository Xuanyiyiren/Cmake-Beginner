cmake_minimum_required(VERSION 3.10)

# 声明工程名称
project(Cmake_Version2)

# 讲当前目录./ 下的所有源码文件保存到 SRC_LIST 变量中
aux_source_directory(. SRC_LIST)

# 声明生成的目标文件和依赖代码
add_executable(main ${SRC_LIST})