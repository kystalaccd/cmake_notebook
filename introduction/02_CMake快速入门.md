# CMake快速入门

### 一、基本概念

#### 1.1. CMake到底是什么

CMake是一个构建生成器，提供了强大的领域特定语言(`DSL`)来描述构建系统应该实现的功能。这是CMake的主要优势之一，它允许使用相同的CMake脚本集生成平台原生构建系统，也就是说CMake是一个能够在不同平台上配置、构建、测试和部署项目的工具。



#### 1.2. CMake到底是怎么工作的





### 二、将单个源文件编译成可执行文件

假设我们现在有如下的源码需要编译成可执行文件`helloWorld.cpp`：

```c++
#include <iostream>

using namespace std;

int main(void){
    cout<<"Hello, world!"<<endl;
    return 0;
}
```

接下来在<font color=red>与源码文件`helloWorld.cpp`相同的目录下</font>，新建一个文本文件`CMakeLists.txt`，这个文件中将会写入我们构建项目的CMake指令。注意命名只能为`CMakeLists.txt`，并且区分大小写。下面为`CMakeLists.txt`的完整代码：

```cmake
cmake_minimum_required(VERSION 3.5 FATAL_ERROR)	#设置CMake所需的最低版本。如果使用的CMake版本低于该版本，则会发出致命错误
project(firstExe LANGUAGES CXX)	#声明了项目的名称【firstExe】和支持的编程语言【CXX代表C++】
add_executable(helloWorld helloWorld.cpp)	#指示CMake创建一个可执行文件helloWorld, 这个可执行文件是通过编译和链接源文件helloWorld.cpp生成的。CMake将为编译器使用默认设置，并自动选择生成工具
```

目前我们已经有了如下的项目结构：

```bash
$ tree .

.
├── CMakeLists.txt
└── helloWorld.cpp

0 directories, 2 files
```

现在我们可以通过创建`build`目录，在`build`目录下来配置项目：

```bash
$ mkdir build
$ cd build/
$ cmake ..

-- The CXX compiler identification is GNU 7.5.0
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/ubuntu/czx_workdir/CMake_proj/01_base/build
```

项目的配置文件已经生成。我们现在可以编译可执行文件：

```bash
$ cmake --build .

Scanning dependencies of target helloWorld
[ 50%] Building CXX object CMakeFiles/helloWorld.dir/helloWorld.cpp.o
[100%] Linking CXX executable helloWorld
[100%] Built target helloWorld
```

目前我们已经完成了项目的构建，在`build`目录结构如下：

```bash
$ ls -al

total 48
drwxrwxr-x 3 ubuntu ubuntu  4096 Jul 23 00:59 .
drwxrwxr-x 3 ubuntu ubuntu  4096 Jul 23 00:56 ..
-rw-rw-r-- 1 ubuntu ubuntu 11075 Jul 23 00:56 CMakeCache.txt
drwxrwxr-x 5 ubuntu ubuntu  4096 Jul 23 00:59 CMakeFiles
-rw-rw-r-- 1 ubuntu ubuntu  1538 Jul 23 00:56 cmake_install.cmake
-rwxrwxr-x 1 ubuntu ubuntu  8928 Jul 23 00:59 helloWorld
-rw-rw-r-- 1 ubuntu ubuntu  5013 Jul 23 00:56 Makefile
```

其中的`helloWorld`即为我们生成的可执行文件，我们运行一下：

```bash
$ ./helloWorld 

Hello, world!
```

与我们预期的一样！接下来解释一下CMake在`build`目录下生成的各文件（夹）的作用：

* `CMakeCache.txt`：CMake缓存。CMake在重新运行配置时将会使用这个文件；
* `CMakeFiles`：包含临时文件的目录，CMake用于检测操作系统、编译器等。此外，根据所选的生成器，它还包含特定的文件；
* `cmake_install.cmake`：处理安装规则的CMake脚本，在项目安装时使用；
* `Makefile`：`make`将运行指令来构建项目，选择不同的生成器此处将会不同，默认情况下，在GNU/Linux系统上，CMake使用`Unix Makefile`生成器。



### 三、略微复杂的项目的构建



