#### 1 简介

展示一个源码和包含文件目录不同的示例，这个教程包含下列文件：

```bash
B-hello-headers$ tree
.
├── CMakeLists.txt
├── include
│   └── Hello.h
└── src
    ├── Hello.cpp
    └── main.cpp
```

其中：

- `CMakeLists.txt`：包含即将运行的cmake命令；
- `src/Hello.h`：要包含的头文件；
- `src/Hello.cpp`：要编译的源码；
- `main.cpp`：包含`main`的源码。



#### 2 概念

##### 2.1 目录路径

CMake语法指定了一些能用来找到工程下有用目录的变量，包括：

| 变量                       | 信息                                     |
| -------------------------- | ---------------------------------------- |
| `CMAKE_SOURCE_DIR`         | 源码根目录                               |
| `CMAKE_CURRENT_DIR`        | 使用子工程/目录时的当前源码目录          |
| `PROJECT_SOURCE_DIR`       | 当前CMake工程的源码目录                  |
| `PROJECT_BINARY_DIR`       | 根二进制/构建目录，是运行CMake命令的目录 |
| `CMAKE_CURRENT_BINARY_DIR` | 当前所在的构建目录                       |
| `PROJECT_BINARY_DIR`       | 当前工程的构建目录                       |

##### 2.2 源文件变量

创建包含源文件的变量能对这些文件更清晰，并更方便地添加到一些命令中：

```cmake
# Create a sources variable with a link to all cpp files to compile
set(SOURCES
    src/Hello.cpp
    src/main.cpp
)
add_executable(${PROJECT_NAME} ${SOURCES})
```

> 注意：另一种在`SOURCES`变量中设定特定文件的方法是使用`GLOB`命令通过通配符模式匹配来找到文件：
>
> ```cmake
> file(GLOB SOURCES "src/*.cpp")
> ```
>
> 建议：在现代CMake中并不推介对源文件使用变量，而是在`add_xxx`函数中直接声明，这对`glob`命令尤其重要，因为它可能并不总是展示正确的结果。

##### 2.3 包含目录

当有不同的`include`目录时，可以使用`target_include_directories()`函数来使编译器意识到它们，当编译这些目标时会使用`-I`标识（如`-I/directory/path`）来添加这些目录到编译器：

```cmake
target_include_directories(target
    PRIVATE
        ${PROJECT_SOURCE_DIR}/include
)
```

其中的`PRIVATE`标识符指定了这个包含的视野（scope），这个标识符十分重要，会在下个示例中解释。



#### 3 构建示例

##### 3.1 标准输出

构建示例的标准输出入下面所示：

```bash
$ mkdir build

$ cd build

$ cmake ..
-- The C compiler identification is GNU 4.8.4
-- The CXX compiler identification is GNU 4.8.4
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build

$ make
Scanning dependencies of target hello_headers
[ 50%] Building CXX object CMakeFiles/hello_headers.dir/src/Hello.cpp.o
[100%] Building CXX object CMakeFiles/hello_headers.dir/src/main.cpp.o
Linking CXX executable hello_headers
[100%] Built target hello_headers

$ ./hello_headers
Hello Headers!
```

##### 3.2 冗余输出

标准输出只输出构建的状态，若要查看调试过程的全部输出，可以在运行make的时候添加`VERBOSE=1`，输出结果如下，它展示了包含目录被添加到C++编译器命令：

```bash
$ make clean

$ make VERBOSE=1
/usr/bin/cmake -H/home/matrim/workspace/cmake-examples/01-basic/hello_headers -B/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build --check-build-system CMakeFiles/Makefile.cmake 0
/usr/bin/cmake -E cmake_progress_start /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles/progress.marks
make -f CMakeFiles/Makefile2 all
make[1]: Entering directory `/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build'
make -f CMakeFiles/hello_headers.dir/build.make CMakeFiles/hello_headers.dir/depend
make[2]: Entering directory `/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build'
cd /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build && /usr/bin/cmake -E cmake_depends "Unix Makefiles" /home/matrim/workspace/cmake-examples/01-basic/hello_headers /home/matrim/workspace/cmake-examples/01-basic/hello_headers /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles/hello_headers.dir/DependInfo.cmake --color=
make[2]: Leaving directory `/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build'
make -f CMakeFiles/hello_headers.dir/build.make CMakeFiles/hello_headers.dir/build
make[2]: Entering directory `/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build'
/usr/bin/cmake -E cmake_progress_report /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles 1
[ 50%] Building CXX object CMakeFiles/hello_headers.dir/src/Hello.cpp.o
/usr/bin/c++    -I/home/matrim/workspace/cmake-examples/01-basic/hello_headers/include    -o CMakeFiles/hello_headers.dir/src/Hello.cpp.o -c /home/matrim/workspace/cmake-examples/01-basic/hello_headers/src/Hello.cpp
/usr/bin/cmake -E cmake_progress_report /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles 2
[100%] Building CXX object CMakeFiles/hello_headers.dir/src/main.cpp.o
/usr/bin/c++    -I/home/matrim/workspace/cmake-examples/01-basic/hello_headers/include    -o CMakeFiles/hello_headers.dir/src/main.cpp.o -c /home/matrim/workspace/cmake-examples/01-basic/hello_headers/src/main.cpp
Linking CXX executable hello_headers
/usr/bin/cmake -E cmake_link_script CMakeFiles/hello_headers.dir/link.txt --verbose=1
/usr/bin/c++       CMakeFiles/hello_headers.dir/src/Hello.cpp.o CMakeFiles/hello_headers.dir/src/main.cpp.o  -o hello_headers -rdynamic
make[2]: Leaving directory `/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build'
/usr/bin/cmake -E cmake_progress_report /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles  1 2
[100%] Built target hello_headers
make[1]: Leaving directory `/home/matrim/workspace/cmake-examples/01-basic/hello_headers/build'
/usr/bin/cmake -E cmake_progress_start /home/matrim/workspace/cmake-examples/01-basic/hello_headers/build/CMakeFiles 0
```

