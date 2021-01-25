#### 1 简介

展示一个非常基础的 hello world 示例。教程中的文件组织形式为：

```bash
A-hello-cmake$ tree
.
├── CMakeLists.txt
├── main.cpp
```

其中：

- `CMakeLists.txt`：包含即将运行的cmake命令；
- `main.cpp`：c++源码。

#### 2 概念

##### 2.1 `CMakeLists.txt`

`CmakeLists.txt`文件包含所有cmake命令，当cmake在目录中运行时会查找这个文件，若不存在就会报错退出。

##### 2.2 最小cmake版本

在使用cmake创建工程时，可以指定支持的cmake的最小版本：

```cmake
cmake_minimum_required(VERSION 3.5)
```

##### 2.3 工程

cmake构建可以包含一个工程名，这样在使用多个工程时就能更方便得引用特定变量：

```cmake
project(hello_cmake)
```

##### 2.4 创建可执行文件

`add_executable()`命令指定从特定源码生成的可执行文件，第一个参数是要创建的可执行文件的名字，第二个参数是要编译的源码列表。

```cmake
add_executable(hello_cmake main.cpp)
```

> 注意：一些人会使用相同的名称来命名工程和可执行文件，这种情况下就能如下指定`CMakeLists.txt`文件：
>
> ```cmake
> cmake_minimum_required(VERSION 2.6)
> project (hello_cmake)
> add_executable(${PROJECT_NAME} main.cpp)
> ```

##### 2.5 二进制目录

运行cmake命令的根/顶层目录被称为`CMAKE_BINARY_DIR`，是所有二进制文件的根目录。cmake支持in-place和out-of-source两种构建和生成二机制文件的方式：

- in-place构建在与源码相同的目录下生成临时文件，包括所有的`Makefiles`和目标文件，要执行in-place构建直接在项目的根目录下运行`cmake .`；
- out-of-source构建允许在文件系统的任何地方创建一个单独的构建目录，所有的临时和目标文件都在这个目录中，从而保持源码的整洁。要执行out-if-source构建，在构建目录下运行cmake命令并指定包含`CMakeLists.txt`的目录，比如`mkdir build && cmake ..`。

这个教程的所有示例都使用out-of-source构建。



