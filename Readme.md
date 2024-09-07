# CMake

Makefile 的问题在于平台依赖性太强，因为不同平台实现类似功能的命令不同。CMake可以解决这个问题。

老师的讲解视频讲的太简单了，我准备参照这个[视频](https://www.bilibili.com/video/BV1nw411C71Z/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=e83f6243963f730d4d2994d23f588a9d)学习。依然是使用四个例子完成。

使用的示例源代码包含四个文件

```cpp
// factorial.cpp
#include "functions.h"

int factorial(int n){
    if (n == 1)
        return 1;
    else
        return n * factorial(n - 1);
}
```

```cpp
// printhello.cpp
#include <iostream>
#include "functions.h"
using namespace std;

void printhello(){
    int i = 0;
    cout << "Hello World!" << endl;
}
```

```c
// functions.h
#ifndef _FUNCTIONS_H_
#define _FUNCTIONS_H_
void printhello();
int factorial(int n);
#endif
```

```cpp
// main.cpp
#include <iostream>
#include "functions.h"
using namespace std;

int main(){
    printhello();

    cout << "This is main:" << endl;
    cout << "The factorial of 5 is: " << factorial(5) << endl;
    return 0;
}
```

要使用 Cmake，需要编辑 "CMakeLists.txt" 文件，并使用 "cmake" + " " + PATH 来运行。

# Version1

我们先将四个源码文件放到一起，在同一个目录下编写 CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)

# 声明工程名称
project(Cmake_Version1)

# 声明生成的目标文件和依赖代码
add_executable(main main.cpp factorial.cpp printhello.cpp)
```

其中第一行表明所需要的 cmake 的最低版本要求。第二行声明工程名称，第三行声明生成的目标文件和依赖代码文件。

编写完成后，直接运行 `cmake .`​ （当前目录下运行 CMake）

可以看到生成了非常多的文件，控制台也产生了很多提示信息

```shell
.
├── CMakeCache.txt
├── CMakeFiles
│   ├── 3.10.2
│   │   ├── CMakeCCompiler.cmake
│   │   ├── CMakeCXXCompiler.cmake
│   │   ├── CMakeDetermineCompilerABI_C.bin
│   │   ├── CMakeDetermineCompilerABI_CXX.bin
│   │   ├── CMakeSystem.cmake
│   │   ├── CompilerIdC
│   │   │   ├── CMakeCCompilerId.c
│   │   │   ├── a.out
│   │   │   └── tmp
│   │   └── CompilerIdCXX
│   │       ├── CMakeCXXCompilerId.cpp
│   │       ├── a.out
│   │       └── tmp
│   ├── CMakeDirectoryInformation.cmake
│   ├── CMakeOutput.log
│   ├── CMakeTmp
│   ├── Makefile.cmake
│   ├── Makefile2
│   ├── TargetDirectories.txt
│   ├── cmake.check_cache
│   ├── feature_tests.bin
│   ├── feature_tests.c
│   ├── feature_tests.cxx
│   ├── main.dir
│   │   ├── CXX.includecache
│   │   ├── DependInfo.cmake
│   │   ├── build.make
│   │   ├── cmake_clean.cmake
│   │   ├── depend.internal
│   │   ├── depend.make
│   │   ├── factorial.cpp.o
│   │   ├── flags.make
│   │   ├── link.txt
│   │   ├── main.cpp.o
│   │   ├── printhello.cpp.o
│   │   └── progress.make
│   └── progress.marks
├── CMakeLists.txt
├── Makefile
├── cmake_install.cmake
├── factorial.cpp
├── functions.h
├── main
├── main.cpp
└── printhello.cpp

8 directories, 40 files
```

其他文件我们不关心，可以的看到生成了一个 Makefile 文件（上面第 40 行），我们再运行 `make`​ 命令，就会得到可执行文件 `main`​，运行可以输出预期的结果。

# Version2

可以看到 CMakeLists.txt 中的第三条代码，我们将目标可执行文件放在第一个，将依赖项统统列在后面。这是一种[不健康的行为😰](https://www.bilibili.com/video/BV1rf421R7G8?t=23.6)（我们MC玩家是这样的） 。

```cmake
cmake_minimum_required(VERSION 3.10)

# 声明工程名称
project(Cmake_Version2)

# 讲当前目录./ 下的所有源码文件保存到 SRC_LIST 变量中
aux_source_directory(. SRC_LIST)

# 声明生成的目标文件和依赖代码
add_executable(main ${SRC_LIST})
```

这个版本中，我们的第三条代码，自动将指定目录下的源码文件保存到变量中（变量的使用和 Makefile 类似），这样就不用一个一个手列了。运行方法和结果同 Version1 。

# Version3

但是另一个问题是 CMake 会生成一堆莫名其妙的文件，使得我们的目录变得杂乱不堪。我们可以将他们统一放到同一个目录下，这样就会整洁很多。

那不妨更整洁一点，我们通常把 `.cpp`​ 文件和 `.h`​ 文件分开，分别放到 src 目录和 include 目录下面。我们再建立一个 build 子目录，专门用于放置 CMake 产生的各种文件。同时建立一个 bin 子目录，专门用于存放可执行文件。

在准备执行代码之前，我们的目录应该长这样

```shell
.
├── CMakeLists.txt
├── bin
├── build
├── include
│   └── functions.h
└── src
    ├── factorial.cpp
    ├── main.cpp
    └── printhello.cpp
```

新的 CMakeLists.txt 的内容如下

```cmake
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
```

多出来的第三条代码指定了最终目标可执行文件的位置。第四条代码制定了需要的源码文件的位置是在 src 子目录下，之前用的是当前目录下。第五条代码指定了头文件目录。其他的代码和之前一致。

执行 CMake 命令时，请先 cd 到 build 目录下，然后运行 `cmake ..`​。这样就会使用上一级目录的 CMakeLists.txt 文件，并且产生的文件会被放到 build 目录下。可以看到 Makefile 文件也在这里。我们接着运行 `make`​ 命令，可以看到 可执行文件 main 已经生成在了 bin 目录下。

最终我们的文件目录会长成这样

```shell
.
├── CMakeLists.txt
├── bin
│   └── main
├── build
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   │   ├── 3.10.2
│   │   │   ├── CMakeCCompiler.cmake
│   │   │   ├── CMakeCXXCompiler.cmake
│   │   │   ├── CMakeDetermineCompilerABI_C.bin
│   │   │   ├── CMakeDetermineCompilerABI_CXX.bin
│   │   │   ├── CMakeSystem.cmake
│   │   │   ├── CompilerIdC
│   │   │   │   ├── CMakeCCompilerId.c
│   │   │   │   ├── a.out
│   │   │   │   └── tmp
│   │   │   └── CompilerIdCXX
│   │   │       ├── CMakeCXXCompilerId.cpp
│   │   │       ├── a.out
│   │   │       └── tmp
│   │   ├── CMakeDirectoryInformation.cmake
│   │   ├── CMakeOutput.log
│   │   ├── CMakeTmp
│   │   ├── Makefile.cmake
│   │   ├── Makefile2
│   │   ├── TargetDirectories.txt
│   │   ├── cmake.check_cache
│   │   ├── feature_tests.bin
│   │   ├── feature_tests.c
│   │   ├── feature_tests.cxx
│   │   ├── main.dir
│   │   │   ├── CXX.includecache
│   │   │   ├── DependInfo.cmake
│   │   │   ├── build.make
│   │   │   ├── cmake_clean.cmake
│   │   │   ├── depend.internal
│   │   │   ├── depend.make
│   │   │   ├── flags.make
│   │   │   ├── link.txt
│   │   │   ├── progress.make
│   │   │   └── src
│   │   │       ├── factorial.cpp.o
│   │   │       ├── main.cpp.o
│   │   │       └── printhello.cpp.o
│   │   └── progress.marks
│   ├── Makefile
│   └── cmake_install.cmake
├── include
│   └── functions.h
└── src
    ├── factorial.cpp
    ├── main.cpp
    └── printhello.cpp

13 directories, 40 files
```
