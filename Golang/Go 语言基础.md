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
   ```go
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

## 03 类型与变量

### Go 基本类型
* 布尔型：bool
    * 长度：1 字节
    * 取值范围： true，false
    * 注意事项：不可以用数字 1 / 0 表示
* 整型：int / uint
    * 根据运行平台可能为 32 或 64 位
    * 8 位整型：int8 / uint8 (byte)
        * 长度：1 字节
        * 取值范围：-128 ~ 127 / 0 ~ 255
    * 16 位整型：int16 / uint16
        * 长度：2 字节
        * 取值范围：-32768 ~ 32767 / 0 ~ 65535
    * 32 位整型：int32 (rune) / uint32
        * 长度：4 字节
        * 取值范围：- 2 ^ 16 ~ 2 ^ 16 - 1 / 0 ~ 2 ^ 32 - 1
    * 64 位整型：int64 / uint64
        * 长度：8 字节
        * 取值范围：- 2 ^ 32 ~ 2 ^ 32 - 1 / 0 ~ 2 ^ 64 - 1
    * 足够保存指针的 32 位或 64 位整数型：uintptr
* 浮点型：float32 / float64
    * 长度：4 / 8 字节
    * 小数位：精确到 7 / 15 小数位
* 复数：complex64 / complex128
    * 长度：8 / 16 字节
* 其他值类型：
    * array (数组)、struct (结构)、string (字符串)
* 引用类型：
    * slice (切片)、map (类哈希表)、chan (通道)
* 接口类型：interface
* 函数类型：func

### 类型的零值 & 别名

####零值
* 零值并不等于空值，而是当变量被声明为某种类型后的默认值，通常情况下值类型的默认值为 0，bool 为 false，string 为空字符串。

    ```go
    var a int       // 整型 ----- 0
    var b int32     // 32 位整型 ----- 0
    var c float32   // 浮点型 ----- 0
    var d bool      // 布尔型 ----- false
    var e string    // 字符串 ----- 
    var f [1]int    // 整型数组 ----- [0]
    var g [1]bool   // 布尔数组 ----- [false]
    var h []int     // 切片 ----- []
    ```

#### 别名
* 可以为类型设置中文的别名(演示)
    ```go
    type (
        文本 string
    )
    
    func main() {
        var b 文本
        b = "中文类型名"
        fmt.Println(b)
    }
    
    ```   
注：

从严格意义上讲 ``type newint int`` ，这里的 newint 并不能说是 int 的别名，而只是底层数据结构相同，在这里称为自定义类型，在进行类型转换时，仍旧需要显式转换。但是 byte 和 rune 确确实实为 uint8 和 int32 的别名，可以互相进行转换。 
  
### 变量的声明与赋值

#### 单个变量的声明与赋值
* 变量声明格式：``var <变量名称> <变量类型>``
* 变量赋值格式：``<变量名称> = <表达式>``
* 声明同时赋值：``var <变量名称> [变量类型] = <表达式>``
  
  ```go
  // 变量的声明
  var a int
  // 变量的赋值
  a = 123
  
  // 声明变量同时赋值
  var b int = 123
  
  // 省略变量类型的声明和赋值，类型由系统推断
  var c = 321
  
  // 最简写法
  d := 456
  ```

#### 多个变量的声明与赋值
* 全局变量的声明可以使用 ``var()`` 的方式进行简写
* 全局变量的声明不可以省略 ``var`` ，但可以使用并行方式
* 所有变量都可以使用类型推断
* 局部变量不可以使用 ``var()`` 的方式简写，只能使用并行方式
    
   ```go
   var (
        // 使用常规方式
        aaa      = "hello"
        // 使用并行方式以及类型判断
        sss, bbb = 1, 2
        // ccc := 3 // 错误，在 'var' 关键字中使用 ':' 是画蛇添足的
   )
   ```
  
   ```go
   // 多个变量的声明
   var a, b, c, d int
   // 多个变量的赋值
   a, b, c, d = 1, 2, 3, 4
   
   // 多个变量声明的同时赋值
   var e, f, g, h int = 5, 6, 7, 8
   
   // 省略变量恶习，由系统判断
   var i, j, k, l = 9, 10, 11, 12
   
   // 多个变量声明与赋值的最简写法
   m, n, o, p := 13, 14, 15, 16
   ```
  
#### 变量的类型转换
* Go 中不存在隐式转换，所有类型转换必须显示声明
* 转换只能发生在两种兼容的类型之间
    * float 和 int 之间的转换只是精度差异，可以转换
    * bool 和 int 的转换是非法的
* 类型转换的格式：``<ValueA> [:]= <TypeOfValueA>(<ValueB>)``

## 04 常量与运算符

### 常量

#### 常量的定义
* 常量的值在编译时就已经确定
* 常量的定义格式与变量基本相同
* 等号右侧必须是常量或者常量表达式
* 常量表达式中的函数必须是内置函数
* 定义常量尽量全部大写，提高代码质量
    * 如果不希望定义的常量被外部包使用到，可以在常量前加上 '_' 或 'c' 
  ```go
  // 定义单个常量
  const a int = 1
  const b = 'A'
  const (
  	text   = "123"
  	length = len(text)
  	num    = b * 20
  )
  
  // 同时定义多个常量
  const i, j, k = 1, "2", '3'
  const (
  	text2, length2, num2 = "456", len(text2), k * 10
  )
  ```

#### 常量的初始化规则与枚举
* 在定义常量组时，如果不提供初始值，则表示将用上行的表达式
* 使用相同的表达式不代表具有相同的值
* ``iota`` 是常量的计数器，从 0 开始，组中每定义 1 个常量自动递增 1
* 通过初始化规则与 ``iota`` 可以达到枚举的效果
* 每遇到一个 ``const`` 关键字， ``iota`` 就会重置为 0
  ```go
  const(
      // a 与 b 都为 "A"
      a = "A"
      b
      c = iota
      // d 的值为 3
      d 
  )
  
  const(
      e = iota
      // f 的值为 1
      f
  )
  ```
  
  ```go
  // 星期枚举
  const (
      // 第一个常量不可省略表达式
      Monday = iota
      Tuesday
      Wednesday
      Thursday
      Friday
      Saturday
      Sunday
  )
  ```

### 运算符
Go 中的运算符均是从左至右结合

优先级(从高到低)

|优先级|运算符及其使用|
|:---:|:---|
|1|^ : 一元运算符，包括符号位在内按位取反<br>! : 取反|
|2|* : 乘法<br>/ : 除法<br>% : 取余<br><< : 左移<br>>> : 右移<br>& : 与 AND<br>&^ : 位清空 AND NOT 
|3|+ : 加法<br>- : 减法<br> \| : 或 OR<br>^ : 二元运算符，异或 XOR<br>|
|4|== : 等于<br>!= : 不等于<br>< : 小于<br><= : 小于等于<br>>= : 大于等于<br>> : 大于<br> |
|5|<- : 并发中专门用于 channel|
|6|&& : A && B 结果为真要求：A、B 都为真，如果 A 为假，则不计算 B(短路)|
|7|\|\| : A\|\|B 运算结果为假要求：A，B 都为假，如果 A 为真，则不计算 B(短路)|
