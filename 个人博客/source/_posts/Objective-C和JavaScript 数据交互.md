title: Objective-C和JavaScript 数据交互
date: 2016-04-07 
categories: iOS
tags: [JavaScriptCore]

---

#### <s>iOS7 之前</s>

##### OC 调用 JS

在`iOS7`以前，在`OC`中调用`JavaScript`的方式只有一种，就是通过`UIWebView`对象的`stringByEvaluatingJavaScriptFromString:`方法，可以实现的交互很少，还需要JS提供相应的方法，实现功能，常用代码如下：

<!-- more --> 


```objc
NSString *title =[webView stringByEvaluatingJavaScriptFromString:@"document.title"];  //得到网页标题
```

##### JS 调用 OC

通过`UIWebView`的重定向，通过在`UIWebView`的代理函数`webView:shouldStartLoadWithRequest:navigationType:`来监听`URL`的变化，这个函数会在`webview`加载`URL`时回调，我们可以`return YES`来让`webview`继续加载内容，`return NO`来停止继续加载新的内容。要约定好几个虚假的URL(比如`tool://goToHomePage`)，我们拿到定义好的`URL`之后做对应的`Objective-C`函数调用。

比如我们在`JavaScript`文件里添加点击响应的函数

	function clickButton() {
	
	      window.location = "tool://goToHomePage"
	
	}

然后在`webview`的代理函数中监听，如果是事先定义好的虚假`URL`，就进行对应的处理

```objc
-(BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    NSURL *url = request.URL;
    if ([[url scheme] isEqualToString:@"tool"] && [[url host] isEqualToString:@"goToHomePage"]) {
      //添加跳转到HomePage的逻辑
       [self jumpToHomePage];
       return NO;
    }
    return YES;

}
```

可以看出这种做法非常笨重，效率低下，无法实现混合开发。接下来要说到这篇博客的重点，利用`JavaScriptCore`实现`OC`和`JS`之间的深度交互。

#### JavaScriptCore

##### 类概述

`JavaScriptCore`头文件中包括以下几类：

	#import "JSContext.h"
	#import "JSValue.h"
	#import "JSManagedValue.h"
	#import "JSVirtualMachine.h"
	#import "JSExport.h"

###### JSContext

`JavaScript`的运行环境，你需要用`JSContext`来执行`JavaScript`代码。

###### JSValue

`JavaScript`实体，`JSValue`可以表示很多`JavaScript`原始类型例如*boolean*, *integers*, *doubles*，甚至包括对象，每个`JSValue`都是强引用一个`JSContext`。

###### JSManagedValue

内存管理辅助对象，用来避免循环引用。本质上属于`JSValue`实例。

###### JSVirtualMachine

`JS`运行的虚拟机，有独立的堆空间和垃圾回收机制。

###### JSExport

协议，`JS`对象直接调用`OC`对象的属性及方法必须实现的协议。

##### OC 调用 JS

先加入`JavaScriptCore`的头文件。

	#import <JavaScriptCore/JavaScriptCore.h>

实例代码，`OC`调用`JS` 实现 两个数字相加

```objc
// 初始化对象
JSContext *context = [[JSContext alloc]init];
// JS 脚本
NSString *function = @" var sum = function(a,b){ "
                         "      return a+b;         "
                         "                        } ";
   // 将脚本加入到context中                      
[context evaluateScript:function];
   
JSValue *jsFunction = context[@"sum"];
   // 利用JSValue调用 
JSValue *sum = [jsFunction callWithArguments:@[@1,@2]];
 // 转换类型 
NSInteger result = [sum toInt32];
  
NSLog(@"%@,%ld",sum,result); // sum = 3
```

`JSValue` 包括一系列方法用于访问其可能的值以保证有正确的 `Foundation` 类型，包括：

| JavaScript Type | JSValue method | Objective-C Type |
|:---|:---|:---|
|string |	toString |	NSString
|number |	toNumber <br>toDouble<br> toInt32<br> toUInt32|NSNumber<br>double<br>int32_t<br>uint32_t 
|Date |	toDate| 	NSDate
|Array| toArray |	NSArray
|Object |	toDictionary |	NSDictionary
|Object |	toObject <br>toObjectOfClass:|

##### JS 调用 OC

JS调用OC有两个方法：`Block`和`JSExport`。

###### Block

<s>稍微复杂的运算</s>给出一个数字，从1加到当前数字，将此函数在OC中实现，在JS中调用，代码如下：

```objc
// 初始化对象
JSContext *context = [[JSContext alloc]init];
// JS 脚本
NSString *function = @"var sum = function (){ return gauss(100);};";
   
[context evaluateScript:function];
   
context[@"gauss"] = ^(NSInteger a) {
   
       NSInteger sum = 0;
       
       for (NSInteger i = 0; i <= a; i ++) {
           
           sum += i;
       }
       
       return sum;
   };
   
   JSValue *value = context[@"sum"];
   JSValue *result = [value  callWithArguments:@[]];
   
   NSLog(@"%@",result); // result = 5050
```

我们可以得到JS执行上下文，利用JS语句得到相对应的**DOM对象**，运行OC中自定义的方法，完美。

###### JSExport

`JSExport`是一个协议，可以让原生类的属性或方法转化为`JavaScript`的属性或方法。需要我们自定义一个协议继承`JSExport`，然后我们在签署我们自定义的协议。代码如下：

	@protocol MyExport <JSExport>
	
	@end
	
	@interface ViewController : UIViewController <MyExport>
	
	@end

例如有如下`JavaScript`代码

```c
function Person(name, age) {
    this.name = name;
    this.age = age;
}
var Persons = new Array;
function addPerson(p) {
    Persons.push(p);
}
```

可以在`Objective-C`中把`Person`对象传递给`addPerson`函数

```objc
Person *aPerson = [[Person alloc] init];
aPerson.name = @"Jone";
aPerson.age = @25;
JSContext *context = [[JSContext alloc]init];    
[context evaluateScript:JSString];
JSValue *function = context[@"addPerson"];
[function callWithArguments:@[aPerson]];
```

##### 异常处理

```objc
context.exceptionHandler = ^(JSContext *context, JSValue *exception) {
       NSLog(@"Error: %@", exception);
};
```

##### 内存管理

`Objective-C`的内存管理机制是引用技术，`JavaScript`的内存管理机制是垃圾回收。由于每个`JSValue`都是强引用一个`JSContext`，可能会存在循环引用的情况。<s>理解不深，不便多言。</s>

#### 参考资料

- [iOS — Derek Liu](https://www.gitbook.com/book/liuduo1988/ios/details)
- [Java​Script​Core](http://nshipster.cn/javascriptcore/)
- [JavaScriptCore 使用](http://www.jianshu.com/p/a329cd4a67ee)
