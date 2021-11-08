# Go 编程基础

## 01 Go 开发环境搭建

### 什么是 Go ？
Go 是一门**并发支持、垃圾回收**和**编译型系统编程语言**，旨在创造一门具有在静态编译语言的**高性能**和动态语言的**高效开发**之间拥有良好平衡点的一门编程语言。

### Go 的主要特点
* 类型安全和内存安全
* 以非常直观和极低代价的方案实现高并发
* 高效的垃圾回收机制
* 快速编译(同时解决 C 语言中头文件太多的问题)
    * 有编译检查，未使用的包存在时会引发编译异常
* 为多核计算机提供性能提升的方案
* UTF - 8 编码支持

### Go 环境变量与工作目录

安装 Go 完成后，在命令行输入 ``go env`` 会看到环境变量相关的参数，其中下列参数需要了解：
```
C:\Users\Huang>go env
set GOARCH=amd64                            // CPU 架构
set GOBIN=                                  // 工作目录下的 BIN 文件夹
set GOEXE=.exe                              // 生成可执行文件的后缀
set GOHOSTARCH=amd64                        // 交叉编译的 CPU 架构
set GOHOSTOS=windows                        // 交叉编译的系统
set GOOS=windows                            // 当前系统名称
set GOPATH=D:\GOPATH                        // 工作目录
set GOROOT=D:\Go                            // 安装目录
set GOTOOLDIR=D:\Go\pkg\tool\windows_amd64  // 工具目录
```

根据约定，GOPATH 下需要建立 3 个目录：
* bin(存放编译后生成的可执行文件)
* pkg(存放编译后生成的包文件)
* src(存放项目源码)

### Go 常用命令
* ``go get``：获取远程包(需提前安装 git)
* ``go run``：直接运行程序(生成临时文件，在命令行显示结果，便于调试)
* ``go build``：测试编译，检查是否有编译错误
* ``go fmt``：格式化源码(部分 IDE 在保存时自动调用)
* ``go install``：编译包文件并编译整个程序
* ``go test``：运行测试文件
* ``go doc``：查看文档

### 第一个 Go 程序
```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("Hello World!你好，世界！")
}
```

## 02 Go 基础知识

### Go 的内置关键字 & 注释方法

#### 关键字：
25个，均为小写
<table>
    <tr>
        <td>break</td>
        <td>default</td>
        <td>func</td>
        <td>interface</td>
        <td>select</td>
    </tr>
    <tr>
        <td>case</td>
        <td>defer</td>
        <td>go</td>
        <td>map</td>
        <td>struct</td>
    </tr>
    <tr>
        <td>chan</td>
        <td>else</td>
        <td>goto</td>
        <td>package</td>
        <td>switch</td>
    </tr>
    <tr>
        <td>const</td>
        <td>fallthrough</td>
        <td>if</td>
        <td>range</td>
        <td>type</td>
    </tr>
    <tr>
        <td>continue</td>
        <td>for</td>
        <td>import</td>
        <td>return</td>
        <td>var</td>
    </tr>
</table>

#### 注释
* ``//``：单行注释
* ``/* */``：多行注释

### Go 程序的一般结构
* Go 程序是通过 ``package`` 来组织的(与 Python 类似)
* 只有 ``package`` 名称为 ``main`` 的包可以包含 ``main`` 函数
* 一个可执行程序有且仅有一个 ``main`` 包


* 通过 ``import`` 关键字导入其他非 ``main`` 包
* 通过 ``const`` 关键字进行常量的定义
* 通过在函数体外部使用 ``var`` 关键字来进行全局变量的声明与赋值
* 通过 ``type`` 关键字进行结构(``struct``)或者接口(``interface``)的声明
* 通过 ``func`` 关键字进行函数的声明

```go
// 当前程序的包名，package 必须放在非注释的第一行
package main

// 导入其他的包
import (
	"fmt"
)

// 常量的定义
const PI = 3.14

// 全局变量的声明与赋值
var name = "gopher"

// 一般类型声明
type newType int

// 结构声明
type gopher struct{}

// 接口的声明
type golang interface {}

// 由 main 函数作为程序入口点启动
func main() {
	fmt.Println("Hello World!你好，世界！")
}
```

#### 导入 package
1. 导入多个包格式
    ```
   improt(
        "<PackageName1>"
        "<PackageName2>"
        ...
   )
   ```
2. 导入包之后，可以使用 ``<PackageName>.<FuncName>`` 来对包中的函数进行调用
3. 如果导入包之后未调用其中的函数或者类型将会报出编译错误： ``imported and not used:"<PackageName>"``
4. 为包起别名： ``improt(<NickName> "<PackageName>")``，此时调用语句变为``<NickName>.<FuncName>``
5. 省略调用：``improt(. "<PackageName>")``
    * 不建议使用，易混淆
    * 不可以和别名同时使用
       
### Go 的可见性规则
* Go 语言中，使用大小写来决定该常量、变量、类型、接口、结构或函数是否可以被外部包所调用：
    * 函数名首字母小写即为 private
    * 函数名首字母大写即为 public
    
