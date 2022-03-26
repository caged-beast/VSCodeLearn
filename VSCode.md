# VSCode学习笔记

## VSCode下载安装

1. 官网下载 [VSCode官网](https://code.visualstudio.com/)
2. 安装很简单，一路默认即可
3. 在文件->首选项->设置中可以设置字体和自动保存（这里可以选onWindowChange,即当窗口失去焦点时自动保存）
4. 语言包不能禁用，可以在Ctrl+Shift+p中搜索display，Configure Diaplay Language可以切换语言

## VSCode使用技巧

### 1.交互式演练场

## VSCode + Markdown

### 1.下载相关的Markdown插件[^1]

1. Markdown All in One（支持键盘快捷键、目录、自动预览等）
2. Markdown Preview Github Styling（把预览改成Github风格）
3. Markdown Preview Enhanced（支持脚注等）
4. markdownlint（检查语法）
5. Markdown Table（支持表格的一些操作）[^2]

### 2.创建存放md文件的文件夹

1. 建议先创建一个文件夹专门用来存放md文件
2. 在VSCode左侧的资源管理器中打开刚刚创建的文件夹，在这里新建md文件

## VSCode + C/C++

### 搭建开发环境

#### 1.下载安装mingw-w64编译器

+ MinGW（Minimalist GNU for Windows），是GCC编译器和GNU Binutils[^3]在Windows平台的移植版本，MinGW 只能编译生成32位可执行程序，而 MinGW-w64 则可以编译生成 64位 或 32位 可执行程序
+ 下载过程：Mingw-w64官网下载太慢，推荐下载离线包：进入[SourceForge中的对应板块](https://sourceforge.net/projects/mingw-w64/files/)->下滑找到MinGW-W64 Gcc-xxx，点击下方 x86_64-win32-sjlj ->如果没有开始下载则点击Downloads进行下载
+ 安装过程：下载的不是安装包，不需要安装，把下载的压缩文件进行解压即可，然后把mingw64的bin目录添加到PATH环境变量

#### 2.下载安装CMake

+ 下载过程：进入[CMake官网](https://cmake.org/)->Download->下滑找到以windows-x86_64.msi为后缀的文件包，点击下载
+ 安装过程：傻瓜式安装，注意把CMake加入PATH环境变量即可

#### 3.VSCode插件安装

1. C/C++（智能提示、调试、代码导航）
2. CMake（对CMake语言的智能提示）
3. CMake Tools（更多的对CMake的支持）

### 流程

#### 1.简单程序的运行

1. 新建一个文件夹来保存程序
2. 打开该文件夹并把它添加到工作区
3. 在该文件夹下新建C/CPP文件，编写完成后保存
4. 终端->新建终端，调出命令行，用g++进行编译，如`g++ hello.cpp -o hello`
5. 输入命令运行，如`.\hello`[^4]

#### 2.简单程序的调试程序

刚刚的编译命令生成的是不带调试信息的可执行文件，要想对程序进行调试的话要在g++命令中加入-g 参数。

1. 点击VSCode左侧的“运行和调试”选项->创建launch.json文件->选择C++(GDB/LLDB)->选择g++.exe
2. 打断点->按F5进行调试

#### 3.多文件程序的运行调试

刚刚是单文件的程序，如果面对的是带main.cpp、xxx.cpp、xxx.h 这样的多文件的程序，运行调试的步骤会有所不同。

1. 用g++对多个.CPP文件进行编译，如`g++ main.cpp xxx.cpp -o xxx`
2. 创建launch.json文件（如果调试失败，把原来的launch.json文件中的program后的内容改为cwd后的内容+系统生成的.exe文件名[^5]，比如cwd后面的内容是 \${workspaceFolder}，系统生成的.exe文件名是main.exe，则把program后的内容改成`${workspaceFolder}\\main.exe`[^6]，并把preLaunchTask一行给注释掉即可）
3. 打断点->按F5进行调试

#### 4.通过CMake进行运行调试

如果面对的是带main.cpp、xxx.cpp、xxx.h 这样的多文件的程序，也可以选择用CMake进行调试

1. 在程序所在目录下新建CMakeLists.txt，写入内容，如：
   
```shell
    project(MYPROJECT) #设置项目名字
    add_executable(main main.cpp swap.cpp) #生成可执行文件（第一个参数是要生成的可执行文件的名字，第二个是生成该文件基于的cpp文件）
```

2. 保存文件，这时会自动生成build文件夹（如果是首次使用即CMake未配置时需要先在命令行搜索进入CMake:Configure，选择gcc编译器，如GCC 8.1.0）
3. 在终端`cd build`进入build目录下
4. `cmake ..`
5. 在终端输入`mingw-32make.exe`[^7]，在build目录下就生成想要的可执行文件main.exe
6. 把原来的launch.json文件中的program后的内容改为cwd后的内容+刚刚生成的exe文件的路径，比如改成`${workspaceFolder}\\build\\main.exe`，并把preLaunchTask一行给注释掉即可
7. 打断点->按F5进行调试

## VSCode + Python

### 1.VSCode插件安装

1. Python（对Python语言的支持）

[^1]:在VSCode左侧的扩展选项中搜索
[^2]:结合Markdown Preview Enhanced的insert table命令一起用
[^3]:GNU Binutils是一系列二进制工具的集合
[^4]:注意windows下的路径里用的是正斜杠/，而Linux下用的是反斜杠\
[^5]:其实就是改成当前要进行调试的程序的路径，cwd是当前工作目录
[^6]:这里要两个反斜杠，前一个转义用
[^7]:注意windows下的make命令和Linux下的不同，它是 mingw-32make.exe
