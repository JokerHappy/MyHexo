title: iOS Runtime 学习
date: 2016-03-18 
categories: iOS
tags: [Runtime]

---

##### Runtime 概述

`Objective-C runtime`是一个实现`Objective-C`语言的`C`库。对象可以用`C`语言中的结构体表示，方法`（methods）`也可以用`C`函数实现。

当程序执行`[object doSomething]`时，并不是直接找到方法调用，而是会将一条消息`（message）`会发送给对象，对象根据消息决定该作出怎样的反应。

<!-- more --> 

`Objective-C` 是一个动态语言，这意味着它不仅需要一个编译器，也需要一个运行时系统来动态得创建类和对象、进行消息传递和转发。理解` Objective-C `的` Runtime `机制可以帮我们更好的了解这个语言，适当的时候还能对语言进行扩展，从系统层面解决项目中的一些设计或技术问题。

`Objective-C`中一切都被设计成了对象，我们都知道一个类被初始化成一个实例，这个实例是一个对象。实际上一个类本质上也是一个对象，在`Runtime`中用结构体表示。

##### Ivar

###### 定义

`Ivar`: 实例变量类型，是一个指向`objc_ivar`结构体的指针

	typedef struct objc_ivar *Ivar;

###### 操作函数

	// 获取所有成员变量
	class_copyIvarList
	
	// 获取成员变量名
	ivar_getName
	
	// 获取成员变量类型编码
	ivar_getTypeEncoding
	
	// 获取指定名称的成员变量
	class_getInstanceVariable
	
	// 获取某个对象成员变量的值
	object_getIvar
	
	// 设置某个对象成员变量的值
	object_setIvar

###### 实例

> 获取成员变量列表

```objc
        Ivar *ivarList = class_copyIvarList([self class], &count);

        for (unsigned int i; i<count; i++) {
           
            Ivar myivar =ivarList[i];

            const char *ivarname =ivar_getName(myivar);

            NSLog(@"ivar----="">%@", [NSString stringWithUTF8String:ivarname]);
         
        }
```

> 访问私有变量

我们知道，OC中没有真正意义上的私有变量和方法，要让成员变量私有，要放在m文件中声明，不对外暴露。如果我们知道这个成员变量的名称，可以通过runtime获取成员变量，再通过getIvar来获取它的值。

	Ivar ivar = class_getInstanceVariable([Model class], "_str1");
	NSString * str1 = object_getIvar(model, ivar);

##### Property

###### 定义

`objc_property_t`：声明的属性的类型，是一个指向`objc_property`结构体的指针

	typedef struct objc_property *objc_property_t;

###### 操作函数

	// 获取所有属性
	class_copyPropertyList
	说明：使用class_copyPropertyList并不会获取无@property声明的成员变量
	
	// 获取属性名
	property_getName
	
	// 获取属性特性描述字符串
	property_getAttributes
	
	// 获取所有属性特性
	property_copyAttributeList

###### 实例

> 获取属性列表

```objc
	objc_property_t *propertyList = class_copyPropertyList([self class], &count);

	for (unsigned int i=0; i<count; i++) {
           
		const char *propertyname =property_getName(propertyList[i]);

		NSLog(@"property----="">%@", [NSString stringWithUTF8String:propertyname]);
       
        }
```