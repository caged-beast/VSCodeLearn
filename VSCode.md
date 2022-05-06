# VSCode学习笔记

## 1.VSCode下载安装

1. 官网下载 [VSCode官网](https://code.visualstudio.com/)
2. 安装很简单，一路默认即可
3. 在文件->首选项->设置中可以设置字体和自动保存（这里可以选onWindowChange,即当窗口失去焦点时自动保存）
4. 语言包不能禁用，可以在Ctrl+Shift+p中搜索display，Configure Diaplay Language可以切换语言

## 2.VSCode使用技巧

### 2.1.交互式演练场

### 2.2.关于设置

1. 设置里User是全局设置，Workspace是当前工作区设置
2. 在Extension->C/C++->C_Cpp: Clang_format_fallback Style中可以设置格式化代码风格
3. 打开设置，点击右上角三点可以查看修改了哪些默认设置
4. 打开设置，在右上角最左边的图标可以进入到设置的json文件，修改json文件和在UI界面设置效果是一样的。比如我想设置当前工作区的C++格式化代码风格：按步骤2找到UI中的设置->点击左边的齿轮，选择copy setting as json->找到当前工作区的workspace.json文件，把刚刚拷贝的键值对粘贴到"settings"对应的字典里

## 3.VSCode + Markdown

### 3.1.下载相关的Markdown插件[^1]

1. Markdown All in One（支持键盘快捷键、目录、自动预览等）
2. Markdown Preview Github Styling（把预览改成Github风格）
3. Markdown Preview Enhanced（支持脚注等）
4. markdownlint（检查语法）
5. Markdown Table（支持表格的一些操作）[^2]

### 3.2.创建存放md文件的文件夹

1. 建议先创建一个文件夹专门用来存放md文件
2. 在VSCode左侧的资源管理器中打开刚刚创建的文件夹，在这里新建md文件

## 4.VSCode + C/C++

### 4.1.搭建开发环境

#### 4.1.1.下载安装mingw-w64编译器(Mac可以跳过这一步)

+ MinGW（Minimalist GNU for Windows），是GCC编译器和GNU Binutils[^3]在Windows平台的移植版本，MinGW 只能编译生成32位可执行程序，而 MinGW-w64 则可以编译生成 64位 或 32位 可执行程序
+ 下载过程：Mingw-w64官网下载太慢，推荐下载离线包：进入[SourceForge中的对应板块](https://sourceforge.net/projects/mingw-w64/files/)->下滑找到MinGW-W64 Gcc-xxx，点击下方 x86_64-win32-sjlj ->如果没有开始下载则点击Downloads进行下载
+ 安装过程：下载的不是安装包，不需要安装，把下载的压缩文件进行解压即可，然后把mingw64的bin目录添加到PATH环境变量

#### 4.1.2.下载安装CMake(先跳过)

+ 下载过程：进入[CMake官网](https://cmake.org/)->Download->下滑找到以windows-x86_64.msi为后缀的文件包，点击下载
+ 安装过程：傻瓜式安装，注意把CMake加入PATH环境变量即可

#### 4.1.3.VSCode插件安装

1. C/C++（智能提示、调试、代码导航）
2. CMake（对CMake语言的智能提示）
3. CMake Tools（更多的对CMake的支持）
4. vscode-icons-mac(支持图标)
5. Bracket Pair Colorizer 2(使嵌套括号带有不同颜色)
6. Code Runner

### 4.2.流程

#### 4.1.简单程序的运行

1. 新建一个文件夹来保存程序
2. 打开该文件夹并把它添加到工作区
3. 在该文件夹下新建C/CPP文件，编写完成后保存
4. 终端->新建终端，调出命令行，用g++进行编译，如`g++ hello.cpp -o hello.out`
5. 输入命令运行，如`.\hello.out`[^4]

#### 4.2.使用Code Runner运行程序（Code Runner只用来运行程序，不能调试）

安装完Code Runner插件后可以使用它来一键运行程序而不用使用命令，但在此之前要在settings.json中进行一定的配置。在其插件详细信息里有相关配置的简单教程。下面给出一份参考配置信息：

```json
    // files.exclude 隐藏某些文件
    "files.exclude": {
        "**/*.dSYM": true,//里*.dSYM匹配的是生成的调试信息
        "**/*.out": true,
    },
    // code-runner.executorMap 配置每种语言的执行器路径
    // $dir: The directory of the code file being run
    // $fileName: The base name of the code file being run, that is the file without the directory
    // $fileNameWithoutExt: The base name of the code file being run without its extension
    "code-runner.executorMap": {
        "c": "cd $dir && gcc $fileName -o $fileNameWithoutExt.out && $dir$fileNameWithoutExt.out",
        //编译单个文件:先cd到当前文件夹下->编译生成.out文件->运行.out文件
        // "cpp": "cd $dir && g++ $fileName -o $fileNameWithoutExt.out && $dir$fileNameWithoutExt.out",
        //编译多个文件
        // "cpp": "cd $dir && g++ $fileName <file_to_link> -o $fileNameWithoutExt.out && $dir$fileNameWithoutExt",
        "cpp": "cd $dir && g++ $fileName myAdd.cpp -o $fileNameWithoutExt.out && $dir$fileNameWithoutExt.out",

    },
    //下面是几个有用的配置
    "code-runner.saveFileBeforeRun": true, 
    "code-runner.clearPreviousOutput": false,
    "code-runner.runInTerminal": true
```

要进行调试必须配置tasks.json和launch.json文件，在官方文档可以看到tasks和launch的教程

1. 可以通过配置tasks.json文件来执行一些命令行任务，（在.vscode文件夹下新建tasks.json文件），下面给出一个tasks.json配置的参考：

```json
{
    "version": "2.0.0",
    "tasks": [
        // 这里配置了两个任务
        // tips:鼠标悬停在属性值上，可以查看简单的描述信息
        // tips:在填写属性信息时，光标移动到冒号后按command+i可以提示可填的属性值
        // 这个任务是编译程序
        {
            "label": "g++ compile",//任务的label
            "command": "g++",//具体的命令
            "type": "shell",//任务类型
            "args": [//命令的参数
                "${file}",//表示当前文件的绝对路径
                "${fileDirname}/myAdd.cpp",
                "-o",
                "${fileDirname}/${fileBasenameNoExtension}.out",
                "-g",
                "-std=c++17"
            ],
            "problemMatcher": [// 定位错误信息
                "$tsc"
            ],
            "presentation": {
                "reveal": "always"
            },
            "group": "build"
        },
        
        // 这个任务是给vscode权限来调用外部终端（如果遇到该权限问题，可以先run task执行这个任务打开权限，否则不用考虑）
        // （如果launch.json中配置了"externalConsole": true，debug时输出会打印在外部终端，vscode可能没有权限来调用）
        {
            "label": "Open Terminal.app",
            "type": "shell",
            "command": "osascript -e 'tell application \"Terminal\"\n do script \"echo now VS Code is able to open Terminal.app\"\n end tell' ",
            "problemMatcher": [],
            "group": "none"
        }
    ]
}
```

配置完tasks.json文件后可以在Terminal->Run Task选择相应的任务进行执行
2. 要进行程序的调试还得配置launch.json文件，（在.vscode文件夹下新建launch.json文件），下面给出一个launch.json配置的参考：

```json
{
    "configurations": [
    {
        "name": "(lldb) Launch",// 类似tasks.json里面的label
        "type": "cppdbg",
        "request": "launch",
        "preLaunchTask": "g++ compile",// 在debug前要执行的任务，和tasks.json中对应编译任务的label对应
        "program": "${fileDirname}/${fileBasenameNoExtension}.out",// debug的对象，之前编译生成的可执行文件的路径，和tasks.json中对应的g++命令-o后面的文件路径对应
        "args": [],// 运行程序需要的参数
        "stopAtEntry": false,
        "cwd": "${fileDirname}",
        "environment": [],
        "externalConsole": false,// 是否调用外部终端
        "MIMode": "lldb"
    }
    ]
}
```

之后就可以进行打断点调试了

简单总结一下：

1. 如果只要运行程序，可以安装Code Runner插件->在settings.json中配置相关的code-runner信息->在要运行的cpp文件右键run
2. 如果要调试程序，要先在tasks.json文件中配置compile编译任务->在launch.json文件中配置调试信息->在要运行的cpp文件打断点->Run—>start debugging或者run without debugging（其实后者的作用就相当于用Code Runner运行程序，只不过Code Runner更方便）

---

*下面是之前的笔记，有点乱，可以不看*

---

#### 4.3.简单程序的调试

刚刚的编译命令生成的是不带调试信息的可执行文件，要想对程序进行调试的话要在g++命令中加入-g 参数。

1. 点击VSCode左侧的“运行和调试”选项->创建launch.json文件->选择C++(GDB/LLDB)->选择g++.exe
2. 打断点->按F5进行调试

#### 4.4.多文件程序的运行调试

刚刚是单文件的程序，如果面对的是带main.cpp、xxx.cpp、xxx.h 这样的多文件的程序，运行调试的步骤会有所不同。

1. 用g++对多个.CPP文件进行编译，如`g++ main.cpp xxx.cpp -o xxx`
2. 创建launch.json文件（如果调试失败，把原来的launch.json文件中的program后的内容改为cwd后的内容+系统生成的.exe文件名[^5]，比如cwd后面的内容是 \${workspaceFolder}，系统生成的.exe文件名是main.exe，则把program后的内容改成`${workspaceFolder}\\main.exe`[^6]，并把preLaunchTask一行给注释掉即可）
3. 打断点->按F5进行调试

#### 4.5.通过CMake进行运行调试

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

## 5.VSCode + Python

### 5.1.VSCode插件安装

1. Python（对Python语言的支持）

[^1]:在VSCode左侧的扩展选项中搜索
[^2]:结合Markdown Preview Enhanced的insert table命令一起用
[^3]:GNU Binutils是一系列二进制工具的集合
[^4]:注意windows下的路径里用的是正斜杠/，而Linux下用的是反斜杠\
[^5]:其实就是改成当前要进行调试的程序的路径，cwd是当前工作目录
[^6]:这里要两个反斜杠，前一个转义用
[^7]:注意windows下的make命令和Linux下的不同，它是 mingw-32make.exe
