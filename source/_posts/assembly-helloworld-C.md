title: Win10下，将C++编译为WebAssembly并在Web端运行
date: 2020-01-30 19:18:43
---
环境：win10
 
# 一、环境准备
安装Emscripten编译器，用于将C++程序编译为WebAssembly字节码

### 1.安装python-2.7.15.amd64.msi
链接:https://pan.baidu.com/s/1ysY-XNtdpCs2Lg9XS8HDhg  密码:wj0r

### 2.安装emsdk-1.35.0-full-64bit.exe
<!--more-->
链接:https://pan.baidu.com/s/1ysY-XNtdpCs2Lg9XS8HDhg  密码:wj0r

### 3.运行emsdk_env.bat，自动配置环境变量
### 4.验证结果 
```
emcc -v
```

输出如下则环境准备成功
{% asset_img 1.png This is 1.png %}


# 二、C++程序准备
准备C++程序，以hello world为例

### 1.安装vs2019

### 2.新建项目，编写hello.cpp，内容如下

```
#include <iostream>
int main() {
    using namespace std;
	  cout << "hello world";
	  return 0;
}
```

# 三、将C++程序编译为WebAssembly，并输出为html
```
emcc hello.cpp -s WASM=1 -o hello.html
```

* 如需编译为WebAssembly（.wasm文件），则需指定 -s WASM=1 参数；无参则默认编译为asm.js（Javascript代码）

编译完成后，该目录将新增如下文件：
{% asset_img 2.png This is 2.png %}

# 四、启动http的web服务，使html运行其上
```
emrun —no_browser —port 8080 C:\Users\rao\source\repos\hello\hello\hello.html
```
 
* 参数需指定html文件全路径

服务启动成功页面：
{% asset_img 3.png This is 3.png %}

# 四、访问网页
访问 http://localhost:8080/hello.html ，结果如下，即成功在Web端运行C/C++程序
{% asset_img 4.png This is 4.png %}