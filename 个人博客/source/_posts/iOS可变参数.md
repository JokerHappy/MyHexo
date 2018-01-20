title: iOS可变参数
date: 2016-04-06 
categories: iOS
tags: [Notes]

---

##### iOS可变参数

[官方的例子](https://developer.apple.com/library/mac/qa/qa1405/_index.html)，留记

```c
#import <Cocoa/Cocoa.h>

@interface NSMutableArray (variadicMethodExample)

- (void) appendObjects:(id) firstObject, ...; // This method takes a nil-terminated list of objects.

@end

@implementation NSMutableArray (variadicMethodExample)

- (void) appendObjects:(id) firstObject, ...
{
	id eachObject;
	va_list argumentList;
	if (firstObject) // The first argument isn't part of the varargs list,
  {                                   // so we'll handle it separately.
  	[self addObject: firstObject];
  	va_start(argumentList, firstObject); // Start scanning for arguments after firstObject.
  	while (eachObject = va_arg(argumentList, id)) // As many times as we can get an argument of type "id"
	[self addObject: eachObject]; // that isn't nil, add it to self's contents.
  	va_end(argumentList);
  }
}

@end
```

`va_list`：用来保存宏 `va_start` 、`va_arg` 和 `va_end` 所需信息的一种类型。为了访问变长参数列表中的参数，必须声明 `va_list` 类型的一个对象。

`va_start`：访问变长参数列表中的参数之前使用的宏，它初始化用`va_list`声明的对象，初始化结果供宏`va_arg`和`va_end`使用；

`va_arg`：展开成一个表达式的宏，该表达式具有变长参数列表中下一个参数的值和类型。每次调用 `va_arg` 都会修改，用 `va_list` 声明的对象从而使该对象指向参数列表中的下一个参数。

`va_end`：该宏使程序能够从变长参数列表用宏 `va_start` 引用的函数中正常返回。

> 参数结尾必须加上**nil**，表示参数结束，可以再参数后加上宏 `NS_REQUIRES_NIL_TERMINATION`，用于编译时非**nil**结尾的检查。

	- (void) appendObjects:(id) firstObject, ...NS_REQUIRES_NIL_TERMINATION;