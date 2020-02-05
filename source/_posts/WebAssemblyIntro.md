title: WebAssembly学习笔记（三）初步理解WebAssembly
date: 2020-02-05 18:31:43
---
---


# 一、WebAssembly定义
WebAssembly是一种可以在浏览器中执行的二进制字节码，目前大多数新版本的主流浏览器已支持WebAssembly。由于WebAssembly与机器码相似，因此相比于JavaScript加载执行效率更佳。WebAssembly适用于需要大量计算的场景，如：音视频处理、游戏开发等。

# 二、使用方式
## （一）由AssemblyScript（最佳实践）、C/C++（官方推荐）、TypeScript、kotlin、GoLang、Rust（不推荐）等强类型高级语言编译为WebAssembly
### 1.编译器工具
（1）Emscripten编译器
基于LLVM开发，前端编译器Clang将高级语言翻译为中间代码LLVM IR，后端编译器Fastcomp将LLVM IR翻译为asm.js（JavaScript）并再翻译为wasm（WebAssembly）。
<!--more-->

（2）Binaryen编译器
例如可将AssemblyScript编译为wasm（WebAssembly）。

## （二）在JavaScript代码中调用WebAssembly函数

## （三）在WebAssembly代码中调用JavaScript函数

# 三、前端性能提升历程
{% asset_img history.png This is history.png %}

# 四、WebAssembly的工作原理
## （一）传统JS引擎（处理JavaScript脚本的虚拟机）的工作原理
传统JS引擎最初仅包含解释器，后来加入JIT编译器形成混合模式，效率提升。

### 1.编译器前端
将高级语言翻译为中间代码IR。

### 2.编译器后端
将中间代码IR翻译为特定CPU架构（X86、AMD64、ARM等）的机器码。
{% asset_img 1.png This is 1.png %}

> 扩展：JIT编译器工作原理
> JS引擎通过监视器监控运行信息，根据方法及循环体的执行频率区分为warm/hot。其中，warm代码由基线编译器进行局部优化编译；hot代码由优化编译器根据监视器信息进行优化假设编译，并在执行前进行优化假设检查，如假设正确则执行，假设错误则去优化（丢弃优化代码、回到解释执行/基线编译器编译执行）。

## （二）WebAssembly的工作原理及使用方式详解
### 1.方式一：使用LLVM架构的编译器，将强类型高级语言编译为WebAssembly
（1）前端编译器将高级语言翻译为中间代码LLVM IR
（2）后端编译器将LLVM IR翻译为asm.js（JavaScript）
（3）后端编译器将asm.js翻译为wasm（WebAssembly）
（4）后端编译器将wasm翻译为特定CPU架构（X86、AMD64、ARM等）的机器码
{% asset_img 2.png This is 2.png %}

### 2.方式二：在JavaScript代码中调用WebAssembly函数
由于WebAssembly的参数类型/返回值类型仅可为整型/浮点型，因此在其他类型变量的处理上，WebAssembly将通过手动操作内存地址的方式操作变量。

【例】JavaScript代码中，在将String类型字符串传递给WebAssembly函数时，由于参数类型限制，可将String字符串转换为JavaScript的ArrayBuffer字节数组写入内存，并将ArrayBuffer数组的首元素地址（整型）作为指针参数传递给WebAssembly函数，进而操作String变量。

# 五、JavaScript与WebAssembly的处理阶段对比
下表各处理阶段通常交叉进行：

{% asset_img 3.png This is 3.png %}

# 六、WebAssembly存在的问题
## （一）无法直接操作 DOM
WebAssembly只能通过调用JavaScript函数的方式间接操作DOM，效率较低。

## （二）不包含异常处理

## （三）无自动垃圾回收机制
