# 构建简单的Target

## 一、用于定义Target的三个基本命令

### 01. add_executable()

——该命令用于定义一个可以构建成可执行程序的Target

```cmake
#	<name>	Target的名字，这个参数必须提供
#	[WIN32]	【可选参数】Windows平台特定的参数
#	[MACOSX_BUNDLE]	【可选参数】Mac平台的特定参数
#	[EXCLUDE_FROM_ALL]	【可选参数】如果提供此参数，那CMake默认构建的时候就不会构建这个Target
#	[source]	构建该可执行文件所需的源码

add_executable(<name> [WIN32] [MACOSX_BUNDLE]
		[EXCLUDE_FROM_ALL]
		[source1] [source2...]
)
```



* 例子

```cmake
cmake_minimum_required(VERSION 3.25 FATAL_ERROR)

add_subdirectory(math)
add_subdirectory(math1)
add_subdirectory(mathpro)

add_executable(main main.cpp)	#定义了一个编译成可执行程序的Target，名字为main，其源码只有一个main.cpp

target_link_libraries(main
	PRIVATE
		math0
)
```



### 02. `add_library()`

——该命令用于定义构建成库文件的Target

```cmake
#	[STATIC | SHARED | MODULE]	三个互斥参数，最佳实践是不要自己在CMakeLists.txt中指定这几个参数，而是把主动权交给构建者，通过cmake -DBUILD_SHARED_LIBS=YES的形式传值告诉其需要构建哪种库。
#	STATIC	静态库
# 	SHARED	动态库
#	MODULE	类似于动态库，不过不会被其他库或者可执行程序链接，用于插件式框架的软件的插件构建

add_library(<name> [STATIC | SHARED | MODULE]
	[EXCLUDE_FROM_ALL]
	[<source>...]
)
```



* 例子

```cmake
cmake_mininum_required(VERSION 3.26 FATAL_ERROR)

add_library(study)

message(STATUS "generate ${CMAKE_CURRENT_BINARY_DIR}/study_export.h")
generate_export_header(study)

set_target_properties(study
	PROPERTIES
		VERSION
			${PORJECT_VERSION}
		SOVERSION
			${PROJECT_VERSION_MAJOR}
)

target_sources(study
	PRIVATE
		add.cpp
)

target_include_directories(study
	PUBLIC
		${CMAKE_CURRENT_BINARY_DIR}
		${CMAKE_CURRENT_LIST_DIR}/include
)

install(TARGETS study
	LIBRARY
		DESTINATION
			${CMAKE_INSTALL_LIBDIR}
)

install(FILES ${CMAKE_CURRENT_LIST_DIR}/include/add.h
			${CMAKE_CURRENT_BINARY_DIR}/study_export.h
	DESTINATION
		${CMAKE_INSTALL_INCLUDEDIR}
)
```



### 03. `add_custom_target()`

```cmake
add_custom_target(Name [ALL] 
	[command1 [args1...]]
	[COMMAND command2 [args2...]...]
	[DEPENDS depend depend depend...]
	[BYPRODUCTS [files...]]
	[WORKING_DIRECTORY dir]
	[COMMENT comment]
	[JOB_POOL job_pool]
	[VERBATIM] [USES_TERMINAL]
	[COMMAND_EXPAND_LISTS]
	[SOURCES src1 [src2...]]
)
```



## 二、关于Target链接

Target之间在构建的时候可能会出现A依赖B，B依赖C等情况。一般这种依赖都是因为某个Target需要链接另一个Target。用C/C++的知识来说就是编译器在链接阶段需要链接的库。

在CMake中我们使用`target_link_libraries()`命令来实现这一点。

```cmake
# targetName 该名字必须是一个由add_executable或者add_library()命令创建的Target的名字
# <PRIVATE | PUBLIC | INTERFACE> 这三个选项类似于C++类定义的Public、Private、Protected，这三个选项后面跟着的items一般是一些库的名字，这些库可以是由add_library()命令创建的Target的名字，也可以是其他方式引入的库的名字。
#		PRIVATE——targetName这个target会链接PRIVATE选项后的items指定的这些库，这些库只有targetName这个Target本身需要，其他任何链接targetName这个Target的其他Target都不知道这些items的存在
#		PUBLIC——不止targetName本身这个Target需要这些iterms，其他链接到targetName的Target也需要依赖这些iterms，并链接这些iterms。
#		INTERFACE——targetName本身不需要这些iterms，但是其他链接targetName的Target需要依赖这些iterms，并链接这些iterms

target_link_libraries(targetName
	<PRIVATE | PUBLIC | INTERFACE> item1 [item2...]
	[<PRIVATE | PUBLIC | INTERFACE> iterm3 [item4...]]
)
```



* 例子

```cmake
cmake_minimum_requierd(VERSION 3.26 FATAL_ERROR)

add_subdirectory(study)

add_executable(main)
target_sources(main
	PRIVATE
		main.cpp
)

target_link_libraries(main
	PRIVATE
		study
		nlohmann_json::nlohmann_json
)

install(TARGETS	main
	RUNTIME
		DESTINATION
			${CMAKE_INSTALL_BINDIR}
)
```

