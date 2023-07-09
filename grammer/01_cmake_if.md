# `CMake if`关键字

## 01. `if`控制流程

### 一、语法格式

```cmake
if(<condition>)
	<commands>
elseif(<condition>) #option block, can be repeated
	<commands>
else()	#option block
	<commands>
endif()
```



### 二、基本表达式

#### 2.1. 常量`if(<constant>)`

* 真：`ON, YES, TRUE, Y`或非零数（包括浮点数），则为真
* 假：`OFF, NO, FALSE, N, IGNORE, NOTFOUND`，空字符串，以`-NOTFOUND`结尾，则为假

<font color=red>命名布尔常量不区分大小写</font>



#### 2.2. 变量`if(<variable>)`

* 变量里面的值为**非假值常量**，则为真
* 变量未定义或值为其它，则为假
  * 环境变量总为假
  * 宏参数不是变量



#### 2.3. 字符串`if(<string>)`

* 字符串的值是常量真，则为真
* 其它带引号的字符串始终计算为`false`



### 三、`CMake`脚本示例

```cmake
#201cmake_if/CMakeLists.txt
cmake_minimum_required(VERSION 3.22)
project(cmake_if)
# **************常量测试******************
if(1)
	message("1 is true")
endif()

if(0)
	message("0 is true?")
else()
	message("0 is false")
endif()

if(OFF)
	message("OFF is true?")
elseif(NO)
	message("NO is true?")
else()
	message("OFF and NO are false")
endif()

# *************变量测试**************
if(VAR_NOT_DEF)	# 未定义变量
	message("VAR_NOT_DEF is true?")
else()
	message("VAR_NOT_DEF is false")
endif()

set(VAR1_TRUE TRUE)
if(VAR1_TRUE)	#注意：并不是${VAR1_TRUE}，没有加取值符号
	message("VAR1_TRUE is true")
endif()

# *************字符串测试***************
if("TRUE")
	message("string TRUE is ture!")
endif()

if("ON")
	message("string ON is true!")
endif()

if("test")	#字符串值不是常量真都是false
else()
	message("string test is false")
endif()

if("1234")
	message("string 1234 is true")
endif()
```

> ```shell
> cmake -S . -B build
> ```



## 02. `if`逻辑操作符

### 一、语法格式

```cmake
# NOT, AND, OR
if(NOT<condition>)

if(<cond1> AND <cond2>)

if(<cond1> OR <cond2>)

if((condition) AND (condition OR (condition)))
```



### 二、`CMake`脚本示例

```cmake
cmake_minimum_required(VERSION 3.22)
project(cmake_if)

set(VAR_OFF OFF)
if(NOT VAR_OFF)
	message("NOT VAR_OFF (true)")
endif()

if(TRUE AND ON)
	message("TRUE AND ON is ture")
endif()

if(TRUE OR OFF)
	message("TRUE OR OFF is true")
endif()

if(TRUE AND (TRUE OR OFF))	#运算优先级：先计算括号里面的真假，再计算外面的真假
	message("TRUE AND (TRUE OR OFF) is true")
endif()
```





## 03. `if`判断语句

### 一、基本表达式

#### 1.1. 一元判断

* `EXISTS`：判断是否存在，e.g. 一些文件是否存在
* `COMMAND`：判断后面的内容是不是`CMake`支持的命令
* `DEFINED`：判断变量是否定义了



#### 1.2. 二元判断

* `EQUAL`
* `EQUAL, LESS, LESS_EQUAL, GREATER, GREATER_EQUAL`
* `STREQUAL, STRLESS, STRLESS_EQUAL, STRGREATER, STRGREATER_EQUAL`
* `VERSION_EQUAL, VERSION_LESS, VERSION_LESS_EQUAL, VERSION_GREATER, VERSION_GREATER_EQUAL`
* `MATCHES`：<font color=red>支持正则表达式</font>



#### 1.3. 存在性检查



#### 1.4. 文件操作

