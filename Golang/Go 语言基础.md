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

## 05 控制语句
### 指针
Go 虽然保留了指针，但是与其他编程语言不同的是，在 Go 中不支持指针运算以及 "->" 运算符。而直接采用 " . " 选择符来操作指针目标对象的成员
* 操作符 " & " 取变量地址，使用 " * " 通过指针间接访问目标对象
* 默认值为 nil 而非 NULL

   ```go
   func main() {
       a := 1
       p := &a
       fmt.Println(p)      // 0xc000012088
       fmt.Println(*p)     // 1
   }
   ```

### 递增递减语句
在 Go 当中，``++`` 和 ``--`` 是作为语句而非表达式

### 判断语句 if
* 条件表达式没有括号
* 支持一个初始化表达式(可以是并行方式)
    * if 语句中初始化的变量作用域仅限于 if 语句内
    * 初始化语句中的变量为 block 级别，同时隐藏外部同名变量
      ```go
      func main() {
          a := 10
          if a := 1; a > 0 {
          	fmt.Println(a)  // 1
          }
          fmt.Println(a)      // 10
      }
      ```
* 左大括号必须和条件语句或 else 在同一行
* 支持单行模式

### 循环语句 for
* 只有 for 一个循环语句关键字，支持 3 种形式
   ```go
   func main() {
       a := 1
       for {
           a++
           if a > 3 {
               break
           }
           fmt.Println(a)
       }
       fmt.Println("Over")
   }
   ```
   
   ```go
   func main() {
       a := 1
       for a <= 3 {
           a++
           fmt.Println(a)
       }
       fmt.Println("Over")
   }
   ```
   
   ```go
   func main() {
       a := 1
       for i := 0; i < 3; i++ {
           a++
           fmt.Println(a)
       }
       fmt.Println("Over")
   }
   ```
* 初始化和步进表达式可以使多个值
* 条件语句每次循环都会被重新检查
    * 不建议在条件语句中使用函数，尽量提前计算好条件并以变量或常量替代
* 左大括号必须和条件语句在同一行

### 选择语句 switch
* 可以使用任何类型或表达式作为条件语句
* 不需要写 break，一旦条件符合自动终止
* 如希望继续执行下一个 case ，需要使用 fallthrough 语句
* 支持一个初始化表达式(可以使并行方式)，右侧需要跟分号
* 左大括号必须和条件语句在同一行
   ```go
   func main() {
       a := 1
       switch a {
       case 0:
           fmt.Println("a = 0")
       case 1:
           fmt.Println("a = 1")
       default:
           fmt.Println("None")
       }
       fmt.Println(a)
   }
   ```
   
   ```go
   func main() {
       a := 1
       switch {
       case a >= 0:
           fmt.Println("a >= 0")
           fallthrough
       case a >= 1:
           fmt.Println("a >= 1")
       }
       fmt.Println(a)
   }
   ```
   
   ```go
   func main() {
       switch a := 1; {
       case a >= 0:
           fmt.Println("a >= 0")
           fallthrough
       case a >= 1:
           fmt.Println("a >= 1")
       }
   }
   ```

### 跳转语句 goto、break、continue
* 三个语法都可以配合标签使用
* 标签名区分大小写，若不使用会造成编译错误
* break 与 continue 配合标签可用于多层循环的跳出
* goto 是调整执行位置，与其他 2 个语句配合标签的结果并不相同
    * 建议将与 goto 一起使用的标签放在 goto 之后，保证程序完整性、避免死循环 
   ```go
   func main() {
   LABEL:
       for {
           for i := 0; i < 10; i++ {
               if i > 2 {
                   break LABEL
               } else {
                   fmt.Println(i)
               }
           }
       }
   }
   ```
  

## 06 数组 Array

### 数组的概念及注意事项
* 定义数组的格式：``var <arrayName> [n]<type>``
    * 为数组赋初值时可以使用形如 ``array := [20]int{19:1}`` 的语句通过索引指定初值
    * 定义数组时数组长度的参数可以用 ``[...]`` 代替，此时数组的长度按照所赋初值分配
* 数组的长度也是类型的一部分，因此具有不同长度的数组为不同类型
* 注意区分指向数组的指针与指针数组
* 数组在 Go 中为值类型 (其他语言多用引用类型)
* 数组直接可以使用 ``==`` 或 ``!=`` 进行比较，但不可以用 ``<`` 或 ``>``
* 可以使用 new 来创建数组，此方法返回一个指向数组的指针
* Go 支持多维数组

### Go 语言的冒泡排序演示
```go
package main

import "fmt"

func main() {
    array := [...]int{5, 2, 6, 3, 9}
    fmt.Println(array)

    arrayLen := len(array)
    for i := 0; i < arrayLen; i++ {
        for j := i + 1; j < arrayLen; j++ {
            if array[i] < array[j] {
                tmp := array[i]
                array[i] = array[j]
                array[j] = tmp
            }
        }
    }
    fmt.Println(array)
}
```

## 07 切片 Slice
### 切片的概念和语法
* 可以直接创建或从底层数组获取生成
* 一般使用 ``make()`` 创建
* 使用 ``len()`` 获取元素个数，``cap()`` 获取容量
* ```make([]T,len,cap)```
    * 其中 cap 可以省略，代表和 len 值相同
    * len 表示存储元素个数，cap 代表容量
* 切片本身不是数组，它指向底层的数组
* 作为变长数组的替代方案，可以关联底层数组的局部或全部
* 为引用类型
* 如果多个 slice 指向相同底层数组，其中一个的值改变会影响全部

#### Reslice
* Reslice 时索引以被 Slice 的切片为准
* 索引不可以超过被 Slice 的切片的容量 cap() 值
* 索引越界不会导致底层数组的重新分配而是引发错误

#### Append
* 可以在 Slice 尾部追加元素
* 可以将一个 Slice 追加在另一个 Slice 尾部
    * 如果最终长度未超过追加到 Slice 的容量则返回原始 Slice
    * 如果超过追加到的 Slice 的容量则将重新分配数组并拷贝原始数据
    
#### Copy
* ``copy(to,from)``
* 长切片到短切片会截取
   ```go
   func main() {
       s1 := []int{1, 2, 3}
       s2 := []int{4, 5, 6, 7, 8}
       copy(s1, s2)
       fmt.Println(s1)		// [4 5 6]
   }
   ```
* 短切片到长切片会覆盖
   ```go
   func main() {
       s1 := []int{1, 2, 3}
       s2 := []int{4, 5, 6, 7, 8}
       copy(s2, s1)
       fmt.Println(s2)		// [1 2 3 7 8]
   }
   ``` 

## 08 Map
### Map 的概念和语法
* 类似其他语言中的哈希表或字典，以键值对形式存储数据
* 使用 ``make()`` 创建，也支持 ``:=`` 
    * ``make(map[keyType]valueType,cap)``，cap 代表容量，可省略
    * 超出容量时会自动扩容，但尽量提供一个合理的初始值
    * 使用 ``len()`` 获取元素个数
* Key 必须是支持 ``==`` 或 ``!=`` 比较运算的类型；不可以是函数、map 或 slice
* Map 的查找比线性搜索快得多，但比使用索引访问数据的类型慢 100 倍
* 键值对不存在时自动添加，使用 ``delete()`` 删除键值对
* 使用 ``foe range`` 对 map 和 slice 进行迭代操作

## 09 函数 function
### 函数的概念和语法
* Go 函数不支持：嵌套、重载和默认参数
* Go 函数支持：无需声明原型、不定长度变参、多返回值、命名返回值参数、匿名函数、闭包
    * 不定长变参必须是函数的最后一个参数
    * **一般的参数传入函数是值拷贝，而 slice 传入函数是地址拷贝**
* 定义函数使用关键字 ``func``，且左大括号不能另起一行
* 函数也可以作为一种类型使用

### defer
* 执行方式类似于其他语言中的析构函数，在函数体执行结束后按照调用顺序的相反顺序逐个执行
* 即使用函数发生严重错误也会执行
* 支持匿名函数的调用
* 常用于资源清理、文件关闭、解锁以及记录时间等操作
* 通过与匿名函数配合可在 return 之后修改函数计算结果
* 如果函数体内某个变量作为 defer 时匿名函数的参数，则在定义 defer 时即以及获得了拷贝，否则则是引用某个变量的地址

### panic
* Go 没有异常机制，但有 panic / recover 模式来处理错误
* panic 可以在任何地方引发，但 recover 只有在 defer 调用的函数中有效

## 10 结构 struct
### struct 的概念和使用
* Go 中的 struct 与 C 中的 struct 非常相似，并且 Go 没有 class
* 使用 ``type <Name> struct{}`` 定义结构，名称遵循可见性规则
* 支持指向自身的指针类型成员
* 支持匿名结构，可用作成员或定义成员变量
* 匿名结构也可以用于 map 的值
* 可以使用字面值对结构进行初始化
* 允许直接通过指针来读写结构成员
* 相同类型的成员可以进行直接拷贝赋值
* 支持 ``==`` 与 ``!=`` 比较运算符，不支持 ``>`` 或 ``<``
* 支持匿名字段，本质上是定义了以某个类型名为名称的字段
* 嵌入结构作为匿名字段看起来像继承，但不是继承
* 可以使用匿名字段指针

## 11 方法 method
### method 的概念和使用
* Go 中虽然没有 class，但是依旧有 method
* 通过显示说明 receiver 来实现与某个类型的组合
* 只能为同一个包中的类型定义方法
* Receiver 可以是类型的值或者指针
* 不存在方法重载
* 可以使用值或指针来调用方法，编译器会自动完成转换
* 从某种意义上来说，方法是函数的语法糖
    * 因为 receiver 其实就是方法所接受的第一个参数
    * Method Value vs. Method Expression
* 如果外部结构和嵌入结构存在同名方法，则优先调用外部结构的方法
* 类型别名不会拥有底层类型所附带的方法
* 方法可以调用结构中的非公开字段 

## 12 接口 interface
### interface 的的概念和使用
* 接口是一个或多个方法签名的集合
* 只要某个类型拥有该接口的所有方法签名，即算实现该接口，无需显示声明实现了哪个接口
    * 这称为 Structural Typing
* 接口只有方法声明，没有实现，没有数据字段
* 接口可以匿名嵌入其他接口，或嵌入到结构中
* 类型断言
    * 通过类型断言的 ok pattern 可以判断接口中的数据类型
    * 使用 type switch 则可针对空接口进行比较全面的类型判断
* 接口转换
    * 可以将拥有超集的接口转换为子集的接口

### interface 注意事项
* 将对象赋值给接口时，会发生拷贝，而接口内部存储的是指向这个复制品的指针，既无法修改复制品的状态，也无法获取指针
* 只有当接口存储的类型和对象都为 nil 时，接口才等于 nil
* 接口调用不会做 receiver 的自动转换
* 接口同样支持匿名字段方法
* 接口也可以实现类似 OOP 中的多态
* 空接口可以作为任何类型数据的容器

## 13 反射 reflection
### reflection 的的概念和使用
* 反射可大大提高程序的灵活性，使得 ``interface{}`` 有更大的发挥余地
* 反射使用 ``TypeOf`` 和 ``ValueOf`` 函数从接口中获取目标对象信息
* 反射会将匿名字段作为独立字段(匿名字段本质)
* 想要利用反射修改对象状态，前提是 ``interface.data`` 是 ``settable``，即 ``pointer-interface``
* 通过反射可以"动态"调用方法

## 14 并发 concurrency
### 使用并发前必知
* 很多人都是冲着 Go 大肆宣扬的高并发而跃跃欲试，但是从源码解析来看，goroutine 只是由官方实现的超级"线程池"而已
    * 每个实例 4 - 5 KB 的栈内存占用和由于实现机制而大幅减少的创建和销毁开销，是制造 Go 号称的高并发的根本原因
    * goroutine 简单易用，在语言层面上给予了开发者巨大便利
* 并发并不是并行 —— Concurrency IS NOT Parallelism
    * 并发主要由切换时间片来实现 "同时" 运行
    * 并行直接利用多核实现多线程运行
    * Go 可以设置使用核数，以发挥多核计算机的能力
* goroutine 奉行通过通信来共享内存，而不是共享内存来通信

### Channel
* channel 是 goroutine 沟通的桥梁，大都是阻塞同步的
* 通过 ``make`` 创建，``close`` 关闭
* channel 是引用类型
* 可以使用 ``for range`` 来迭代不断操作 channel
* 可以设置单向或双向通道
* 可以设置缓存大小，在未被填满前不会发生阻塞

### select
* 可处理一个或多个 channel 的发送与接收
* 同时有多个可用的 channel 时按随机顺序处理
* 可用空的 select 来阻塞 main 函数
* 可设置超时

## 15 回顾
### 坑
* slice
    * 如果在方法中使用 ``append``，建议使用返回值来获取，防止切片越界导致重新分配改变指向
* time 的 ``format``
    * 时间格式化时不要自己修改，应使用给出的常量
* ``for range`` 和闭包
    * 不作为参数传递的值都是引用传递