title: Xcode 利用工具解决程序内存问题
date: 2016-02-24 
categories: iOS  
tags: Method

---

苹果在 `iOS 4.2` 时，推出了 `ARC` 的内存管理机制。这是一种编译期的内存管理方式，在编译时，编译器会判断 `Cocoa` 对象的使用状况，并适当的加上 `retain` 和 `release`，使得对象的内存被合理的管理。但是在开发过程中，由于种种原因还是会造成内存泄漏。

> 内存泄漏（Memory Leaks）是当一个对象或变量在使用完成后没有释放掉，这个对象一直占有着这块内存，直到应用停止。如果这种对象过多内存就会耗尽，其它的应用就无法运行。

<!-- more --> 

除了在代码的书写过程中注意内存的管理，还可以利用`Xcode`自带的工具对代码进行分析，解决内存问题。

### Diagnostics

![](http://7xt3bw.com1.z0.glb.clouddn.com/1.png-water)

`Xcode ` 的  `Product` --> `Sheme` --> `edit scheme` --> `Run` -> `Diagnostics` 下有一些选项可以帮助我们找到`app`中的内存问题：

##### Enable Malloc Scribble

> 申请内存后在申请的内存上填`0xAA`，内存释放后在释放的内存上填`0x55`；再就是说如果内存未被初始化就被访问，或者释放后被访问，就会引发异常，这样就可以使问题尽快暴漏出来。
> 
> `Scribble`其实是`malloc`库`libsystem_malloc.dylib`自身提供的调试方案


##### Enable Malloc Guard Edges

>申请大片内存的时候在前后page上加保护，详见[保护模式](http://baike.baidu.com/link?url=47Hbd0Lf8oBC2tUS1HcASJKPWGPYOp3vsUJqJwF4i-6TJ-QkhwIRfKoYpEbnbZFOjavlB383bXTykYOOGVtqya)。

##### Enable Guard Mallocs

> 使用`libgmalloc`捕获常见的内存问题，比如越界、释放之后继续使用。
> 
> 由于`libgmalloc`在真机上不存在，因此这个功能只能在模拟器上使用.

##### Enable Zombie Objects

> `Zombie`的原理是用生成僵尸对象来替换`dealloc`的实现，当对象引用计数为`0`的时候，将需要`dealloc`的对象转化为僵尸对象。如果之后再给这个僵尸对象发消息，则抛出异常，并打印出相应的信息，调试者可以很轻松的找到异常发生位置。

##### Enable Address Sanitizer (Xcode7 +)

> `AddressSanitizer`的原理是当程序创建变量分配一段内存时，将此内存后面的一段内存也冻结住，标识为中毒内存。当程序访问到中毒内存时（越界访问），就会抛出异常，并打印出相应`log`信息。调试者可以根据中断位置和的`log`信息，识别`bug`。如果变量释放了，变量所占的内存也会标识为中毒内存，这时候访问这段内存同样会抛出异常（访问已经释放的对象）。

### 静态分析

一般来说，在程序未运行之前我们可以先通过`Clang Static Analyzer`(静态分析)来检查代码是否存在`bug`。比如，内存泄露、文件资源泄露或访问空指针的数据等。


 + 手动静态分析：每次都是通过点击菜单栏的`Product` --> `Analyze`或快捷键`shift + command + b`

 + 自动静态分析：在`Build Settings`启用`Analyze During 'Build'`，每次编译时都会自动静态分析

![](http://7xt3bw.com1.z0.glb.clouddn.com/2.png-water)

`Analyze`主要分析以下四种问题：

- 逻辑错误：访问空指针或未初始化的变量等；

- 内存管理错误：如内存泄漏等；

- 声明错误：从未使用过的变量；

- `Api`调用错误：未包含使用的库和框架。

### Instruments

使用静态分析能够检查出一些内存泄露问题，但是有时只有运行时使用Instruments才能检查到

**注意：我们需要进行操作让系统不断去执行所要检测的代码是否存在内存泄露。**

启动`Instruments`步骤如下：

+ 点击`Xcode`的菜单栏的 `Product` --> `Profile` 启动`Instruments`。

+ 出现`Instruments`的工具集，选中`Leaks`子工具点击。

+ 打开`Leaks`工具之后，点击红色圆点按钮启动`Leaks`工具，在`Leaks`工具启动同时，模拟器或真机也跟着启动。

+ 启动`Leaks`工具后，它会在程序运行时记录内存分配信息和检查是否发生内存泄露。

+ 在界面的左边栏选择 `Details`-->`Call Trees`。

+ **在界面的右下角有若干选框，选中Invert Call Tree 和Hide System Libraries,（红圈范围内）显示如下**
 
![](http://7xt3bw.com1.z0.glb.clouddn.com/3.png-water)

+ 选中显示的若干条中的一条，双击，会自动跳到内存泄露代码处。

+ 找到了内存泄露的地方，那么我们就可以修改即可。

