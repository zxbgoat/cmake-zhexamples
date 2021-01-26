#### 1 简介

展示一个先创建和连接一个静态库的hello world示例，是一个库和二进制在同一目录下的简化示例，通常应该是在子工程下面。文件组织形式如下：

```bash
$ tree
.
├── CMakeLists.txt
├── include
│   └── static
│       └── Hello.h
└── src
    ├── Hello.cpp
    └── main.cpp
```

- `CMakeLists.txt`：包含即将运行的cmake命令；
- `include/static/Hello.h`：要包含的头文件；
- `src/Hello.cpp`：要编译的源码；
- `src/main.cpp`：包含`main`的源码。



#### 2 概念

##### 2.1 添加一个静态库

`add_library()`函数用于从一些源码文件中创建库，调用方式如下：

```cmake
add_library(hello_library STATIC
    src/Hello.cpp
)
```

这会用`add_library`调用中的源文件创建一个名为`libhello_library.a`的静态库。

>注意：像前一个示例一样，这里将源码文件直接传递到`add_library`调用中，就如现代CMake推介的那样。

##### 2.2 包含目录

这个示例使用`target_include_libraries()`函数以`PUBLIC`的视野包含库中的目录：

```cmake
target_include_directories(hello_library
    PUBLIC
        ${PROJECT_SOURCE_DIR}/include
)
```

这会使被包含的目录在下面的位置用到：

- 在编译这个库时；
- 在编译任何连接到这个库的目标时。

视野的含义为：

- `PRIVATE`：目录被添加到这个目标的包含目录；
- `INTERFACE`：目录被添加到任何连接到这个库的目标的包含目录中；
- `PUBLIC`：被添加到这个目标的包含目录和任何链接到这个库的目标的包含目录；

>建议：
>
>- 对于public头文件，将`include`目录通过子目录命名空间化总是好的；
>
>- 传递给`target_include_directories`的目录是`include`目录树的根，而C++文件应该包含到头文件的路径，在这个示例中为：
>
>  ```c++
>  # include "static/Hello.h"
>  ```
>
>  用这种方法在使用多个库时就不容易产生文件名冲出。

##### 2.3 链接到库

在创建一个需要用到库的可执行文件时，必须将关于库的信息告知编译器，可以使用`target_link_libraries()`函数实现：

```cmake
add_executable(hello_binary
    src/main.cpp
)

target_link_libraries( hello_binary
    PRIVATE
        hello_library
)
```

这个语句告知编译器针对`hello_binary`可执行文件链接`hello_library`，它也会从链接的库目标以`PUBLIC`或`INTERFACE`的视野传播所包含的任意目录。这种情况下由编译器调的一个示例为：

```bash
/usr/bin/c++ CMakeFiles/hello_binary.dir/src/main.cpp.o -o hello_binary -rdynamic libhello_library.a
```



#### 3 构建示例

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
-- Build files have been written to: /home/matrim/workspace/cmake-examples/01-basic/C-static-library/build

$ make
Scanning dependencies of target hello_library
[ 50%] Building CXX object CMakeFiles/hello_library.dir/src/Hello.cpp.o
Linking CXX static library libhello_library.a
[ 50%] Built target hello_library
Scanning dependencies of target hello_binary
[100%] Building CXX object CMakeFiles/hello_binary.dir/src/main.cpp.o
Linking CXX executable hello_binary
[100%] Built target hello_binary

$ ls
CMakeCache.txt  CMakeFiles  cmake_install.cmake  hello_binary  libhello_library.a  Makefile

$ ./hello_binary
Hello Static Library!
```

