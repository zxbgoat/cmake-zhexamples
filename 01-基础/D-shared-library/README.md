#### 1 简介

展示一个首先创建和链接一个共享库的hello world示例，同时也展示了如何创建别名目标，目录结构如下：

```bash
$ tree
.
├── CMakeLists.txt
├── include
│   └── shared
│       └── Hello.h
└── src
    ├── Hello.cpp
    └── main.cpp
```

- `CMakeLists.txt`：包含即将运行的CMake命令；
- `include/shared/Hello.h`：要包含的头文件；
- `src/Hello.cpp`：要编译的源码；
- `src/main.cpp`：包含`main`的源码。



#### 2 概念

##### 2.1 添加一个共享库

像前面静态库的例子一样，`add_library()`函数也能用于从源码创建共享库，调用方式如下：

```cmake
add_library(hello_library SHARED
    src/Hello.cpp
)
```

这会用传递给`add_library`的源码创建一个名为` libhello_library.so`的共享库。

##### 2.2 别名目标

就如其名所表示的那样，别名对象是对象的一个替代名称，能代替真实名称用在只读上下文中：

```cmake
add_library(hello::library ALIAS hello_library)
```

这时在针对其它目标链接到它时时就能使用别名来引用。

##### 2.3 链接一个共享库

链接到共享库和连接到静态库一样，在创建可执行文件时使用`target_link_libraries()`函数来指示库：

```cmake
add_executable(hello_binary
    src/main.cpp
)

target_link_libraries(hello_binary
    PRIVATE
        hello::library
)
```

这种情况下链接器调用方式的一个例子是：

```bash
/usr/bin/c++ CMakeFiles/hello_binary.dir/src/main.cpp.o -o hello_binary -rdynamic libhello_library.so -Wl,-rpath,/home/matrim/workspace/cmake-examples/01-basic/D-shared-library/build
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
-- Build files have been written to: /home/matrim/workspace/cmake-examples/01-basic/D-shared-library/build

$ make
Scanning dependencies of target hello_library
[ 50%] Building CXX object CMakeFiles/hello_library.dir/src/Hello.cpp.o
Linking CXX shared library libhello_library.so
[ 50%] Built target hello_library
Scanning dependencies of target hello_binary
[100%] Building CXX object CMakeFiles/hello_binary.dir/src/main.cpp.o
Linking CXX executable hello_binary
[100%] Built target hello_binary

$ ls
CMakeCache.txt  CMakeFiles  cmake_install.cmake  hello_binary  libhello_library.so  Makefile

$ ./hello_binary
Hello Shared Library!
```

