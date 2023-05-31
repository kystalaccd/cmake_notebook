# CMake变量

## 一、普通变量

> * CMake将所有的变量的值视为字符串
> * 当给出变量的值不包含空格的时候，可以不使用引号，但建议都加上引号，不然一些奇怪的问题很难调试
> * CMake使用空格或者分号作为字符串的分隔符
> * CMake中想要获取变量的值，和shell脚本一样，采用`${var}`形式
> * 使用CMake变量前，不需要这个变量已经定义了，如果使用了未定义的变量，那么它的值是一个空字符串。
>   * 默认情况下使用未定义的变量不会有任何警告信息，但是可以通过CMake的`-warn-uninitialized`选项启用警告信息。
>   * 使用未定义的变量非常常见，如果出现问题也不一定是因为变量未定义导致的，所以CMake的`-warn-uninitialized`选项用处非常有限
> * 变量的值可以包含换行，也可以包含引号，不过需要转义

CMake定义变量的命令如下：

```cmake
# varName	代表要定义的变量的变量名
# value		代表要定义的变量的值，建议用引号引起来。可以是一个以空格或者分号隔开的字符串，这样定义的变量将是一个列表。
# PARENT_SCOPE	【可选参数】意思是定义的这个变量的作用域属于父作用域
set(varName value...[PARENT_SCOPE])
```

CMake取消变量定义：

```cmake
set(myVar)
unset(myVar)
```



* 例子

```cmake
set(CPR_TOP_DIR "/root/workspace/code/cpr")

set(CPR_BASE_SOURCE "a.cpp;b.cpp")

set(CPR_CORE_SOURCE core1.cpp core2.cpp)

set(CPR_BUILD_CMD [[
#!/bin/bash

cmake -S. -B build
cmake --build build
]])

set(shellScript [=[
#!/bin/bash
[[-n "${USER}"]] && echo "Have USER"
]=])
```



## 二、环境变量

> 不管是Windows还是Linux、macOS上，都有环境变量的概念。我们最常使用的环境变量要数`PATH`这个环境变量了。Windows上可以在高级系统设置->环境变量中看到，Linux和macOS上可以使用`echo $PATH`这条命令看到环境变量PATH的值。
>
> 处理PATH这个环境变量，对于在日常的开发中，我们通常还会有自定义的环境变量。比如我们安装了一个第三方软件，然后需要将一些安装目录导出到环境变量，这个时候就可能会用到自定义环境变量。
>
> 有些环境变量，只有我们自己项目需要，所以就没必要为整个系统配置这类环境变量。CMake为我们提供了定义环境变量的方式，这样我们就可以让CMake定义的环境变量只在当前运行的CMake进程中生效，不会影响到系统或者其他进程的环境变量。

CMake定义环境变量和获取环境变量的值用法和普通变量类似，只需要多加一个`ENV`标识符

```cmake
set(ENV{PATH} "$ENV{PATH}:/opt/myDir")
```



## 三、缓存变量

> 缓存变量的值可以缓存到CMakeCache.txt文件中，当再次运行cmake时，可以从中获取上一次的值，而不是重新去评估。所以缓存变量的作用域是全局的。

CMake定义缓存变量的格式如下：

```cmake
# varName	变量名
# value		变量值
# CACHE		定义变量为缓存变量
# type		【必选参数】其值必须是下列之一
#	1. BOOL
#		* BOOL类型的变量值如果是ON、TRUE、1则被评估为真，如果是OFF、FALSE、0则被评估为假
#		* 虽然除了上面列出的值还有其他的值，但是判断真假就没那么清晰了，所以建议定义BOOL类型的缓存变量的是否，其值就采用上述列出的值。虽然不区分大小写，但是建议统一使用大写
#	2. FILEPATH	文件路径
#	3. STRING	字符串
#	4. INTERNAL
#		* 内部缓存变量不会对用户可见，一般是项目为了缓存某种内部信息时才使用，cmake图形化界面工具也对其不可见
#		* 内部缓存变量默认是FORCE的。
# "docstring"	【可以为空】说明性的字符串，只在图形化cmake界面会展示
# FORCE	代表每次运行都强制更新缓存变量的值，如果没有该关键字，当再次运行cmake的时候，cmake将使用CMakeCache.txt文件中缓存的值，而不是重新进行评估

set(varName value... CACHE type "docstring" [FORCE])
```



由于`BOOL`类型的变量使用频率非常高，CMake为其单独提供了一条命令

```cmake
# optVar	变量名
# helpString	【可以为空】提供帮助信息的字符串
# initialValue	【可选参数】代表缓存变量的值，如果没有提供，那该缓存变量的值默认为OFF
# 与set不同的是，option命令没有FORCE关键字
option(optVar helpString [initialValue])

# 等价于

set(optVar initialValue CACHE BOOL helpString)
```



## 四、变量的作用域

