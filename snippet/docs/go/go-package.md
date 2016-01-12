
包
===

## 1、杂项

（1）每个Go程序都是由包组成的。

（2）包是由一个或多个源文件组成的；每个源文件的第一行（除去注释）必须是 `package` 声明语句，以表明该源文件属于哪个包。

（3）包名必须是一个合法的标识符，但不能是 `blank标识符`（即下划线 `_`），`blank标识符`是一个特殊的标识符。

（4）包的声明语法是：
```go
package PackageName
```

（5）具有相同包声明的一系列文件构成了一个包的实现；但是一个实现可能要求属于同一个包的所有源文件位于相同的目录下。


## 2、main包

（1）程序的入口包是“`main`”。

（2）每个GO程序必须有且仅有一个`main包`，并且在`main包`中，有且仅有一个`main函数`，此函数`无参数`、`无返回值`。

（3）程序的执行开始于初始化 `main包`，然后调用 `main函数`；当 `main函数`返回时，程序退出——**`这并不等待其他GO例程完成`**。


## 3、包导入

（1）一个导入声明表明了导入包和被导入包之间的依赖关系。

（2）一个包的导入（`import`）声明表明包含该声明的源文件要依赖于被导入包的功能，并访问被导入包的导出的（`exported`）标识符。

（3）一个包可以直接或间接地导入自身，或者导入一个包，但并不引用被导入包中的任何一个导出的标识符（`exported identifier`）。

（4）按照惯例，包名与导入路径的最后一个目录一致，如：`import "math/rand"`， 则“`math/rand`”包由`package rand`语句开始。

（5）`import` 声明是局部于源文件的，也就是说，它是属于文件块（即文件作用域）的；

一个源文件导入的包，无法在当前包的其他源文件中使用，如果其他源文件要想使用，必须自己导入。

（6）包的导入语法有四种：
```go
import   name  ImportPath
import         ImportPath
import   .     ImportPath
import   _     ImportPath
```
**说明：**

    第一种：导入ImportPath表示的包，然后用 name 作为限定符，被导入包中的所有被导出的（exported）标识符
           都作为限定符 name 的属性来访问。
    第二种：同第一种，但把被导入包中通过package声明语句声明的包名作为限定符。
    第三种：同第一种，但不使用限制符，而是直接让被导入包中的可导出的标识符在当前源文件中可见，
           即不通过限制符直接使用被导入包中的可导出标识符。
    第四种：仅仅是执行被导入包中的初始化函数 init，并不做其它的事情。

（7）**对导入路径的解析是依赖于实现的**。

当前可以使用相对路径（“`.`”），但是相对路径并不被保证在以后的实现中继续支持，因此，应该避免使用相对路径，而是使用“全路径”，全路径并不是绝对路径，它是被编译的源码包的绝对路径的子串。

（8）**`实现限制：`**

一个编译器可能会限制导入路径为一个非空字符串，并且仅使用属于 `Unicode` 的 `L`、`M`、`N`、`P`和`S`等通用种类的字符，但 !"#$%&'()*,:;<=>?[\]^`{|} 等字符（这些字符都是ASCII，并不是其他字符，如：中文字符）以及 Unicode 占位符 `U+FFFD` 除外。

**`建议：`**

导入路径以及包名仅使用 `26个大小写英文字母`、`0到9等十个数字`、`连接符 -` 和 `下划线 _`。


## 4、GO工具包参数

（1）“`main`”表示最顶层的包（在独立的可执行程序中）；

（2）“`all`”表示在`GOPATH`目录树中所有能找到的包目录；

（3）“`std`”表示Go标准库中的包。


## 5、包的初始化

（1）GO语言规范保留了两个函数：`init`（能够应用于所有的包）和 `main`（只能用于main包）

（2）`init` 和 `main` 函数都是`无参数`、`无返回值`。

（3）对于 `init` 有个限制：_**程序中的任何地方都不得引用它，也不能显式地调用它；另外，也不能把 init 的指针赋值给一个函数变量**_。

（4）如果要导入一个包，在导入包自身被初始化之前，被导入包会先被初始化。

（5）如果一个包被多次导入，那么它仅被初始化一次。

（6）对于包的导入，在初始化时不能有循环依赖，也即是，不导入环，如：A 导入 B，B 又导入 A。

（7）`init` 函数是可选的，`可以不定义`，`可以定义一个`，`也可以定义多个`，甚至都定义在同一个源文件中。**_如果定义多个 init 函数，它们将会以不确定的顺序依次执行_**。

（8）包的初始化（包括变量初始化和 `init` 函数调用）发生在一个单独的GO例程中，顺序地执行，一次初始化一个包。

一个 `init` 函数可以会引发其它的GO例程，这些GO例程能够和用来初始化包的GO例程并行执行。

**_`初始化总是按照顺序地、依次执行 init 函数：在上一个 init 函数没有返回之前，下一个 init 函数不会被执行。`_**

（9）程序的初始化和执行都起始于 `main包`。如果 `main包`还导入了其它的包，那么就会在编译时将它们依次导入。当一个包被导入时，如果该包还导入了其它的包，那么会先将其它包导入进来，然后再对这些包中包级常量和变量进行初始化，接着执行 `init函数`（如果有的话），依次类推。等所有被导入的包都加载完毕了，就会开始对 `main包`中的包级常量和变量进行初始化，然后执行 `main包`中的 `init函数`（如果存在的话），最后执行 `main函数`。如下图：

![package init](./_static/package-init.png)

注：第（9）条及上图来源于谢大的《[Go Web 编程](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/02.3.md#main%E5%87%BD%E6%95%B0%E5%92%8Cinit%E5%87%BD%E6%95%B0)》。