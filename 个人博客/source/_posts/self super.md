title: Self 和 Super
date: 2016-04-18 
categories: iOS
tags: [Notes]

---

下面代码输出什么？

<!-- more --> 

```objc
@implementation Son : Father

-(id)init
{
    self = [super init];

    if (self) {

        NSLog(@"%@", NSStringFromClass([self class]));

        NSLog(@"%@", NSStringFromClass([super class]));
    }
    return self;
}

@end

```

> 都输出 `Son`。 `self` 的 `class` 和预想的一样，怎么 `supe`r 的 `class` 也是 `Son`？

`self` 是类的隐藏的参数，指向当前调用方法的类。`super` 并不是隐藏的参数，它只是一个“编译器指示符”，它和 `self` 指向的是相同的消息接收者，拿上面的代码为例，不论是用 `[self class]` 还是 `[super class]`，接收`“class”`这个消息的接收者都是 **当前类** 这个对象。不同的是，`super` 告诉编译器，当调用 `class` 的方法时，要去调用父类的方法，而不是本类里的。

 当使用 `self` 调用方法时，会从当前类的方法列表中开始找，如果没有，就从父类中再找；而当使用 `super` 时，则从父类的方法列表中开始找。然后调用父类的这个方法

 这种机制到底底层是如何实现的？其实当调用类方法的时候，编译器会将方法调用转成一个` C `函数方法调用，`Apple` 的 `objcRuntimeRef` 上说：

```objc
    Sending Messages

    When it encounters a method invocation, the compiler might generate a call to any of several functions to perform the actual message dispatch, depending on the receiver, the return value, and the arguments. You can use these functions to dynamically invoke methods from your own plain C code, or to use argument forms not permitted by NSObject’s perform… methods. These functions are declared in /usr/include/objc/objc-runtime.h.
    ■ objc_msgSend sends a message with a simple return value to an instance of a class.
    ■ objc_msgSend_stret sends a message with a data-structure return value to an instance of
    a class.
    ■ objc_msgSendSuper sends a message with a simple return value to the superclass of an instance of a class.
    ■ objc_msgSendSuper_stret sends a message with a data-structure return value to the superclass of an instance of a class.
```

可以看到会转成调用上面 4 个方法中的一个，由于 `_stret` 系列的和没有` _stret` 的那两个类似，先只关注` objc_msgSend` 和 `objc_msgSendSuper` 两个方法。当使用 `[self class]` 调用时，会使用 `objc_msgSend` 的函数，先看下 `objc_msgSend` 的函数定义：

```objc
id objc_msgSend(id theReceiver, SEL theSelector, ...)

    //第一个参数是消息接收者，第二个参数是调用的具体类方法的 selector，后面是 selector 方法的可变参数。我们先不管这个可变参数，以 [self class] 为例，编译器会替换成调用 objc_msgSend 的函数调用，其中 theReceiver 是 self，theSelector 是 @selector(class)，这个 selector 是从当前 self 的 class 的方法列表开始找的方法，当找到后把对应的 selector 传递过去。
```

而当使用 `[super class]` 调用时，会使用 `objc_msgSendSuper` 函数，看下 `objc_msgSendSuper` 的函数定义：

```objc
id objc_msgSendSuper(struct objc_super *super, SEL op, ...)

    //第一个参数是个objc_super的结构体，第二个参数还是类似上面的类方法的selector，先看下objc_super这个结构体是什么东西：

struct objc_super {
    id receiver;
   Class superClass;
};
```

第一个成员是 `receiver`, 类似于上面的 `objc_msgSend`函数第一个参数`self`
第二个成员是记录当前类的父类是什么，告诉程序从父类中开始找方法，找到方法后，最后内部是使用 `objc_msgSend(objc_super->receiver, @selector(class))`去调用， 此时已经和`[self class]`调用相同了，故上述输出结果仍然返回 `Son`

里面的调用机制大体就是这样了，以上面的分析，回过头来看开始的代码，当输出` [self class]` 和 `[super class]` 时，是个怎样的过程。

> 当使用 `[self class] `时，这时的 `self` 是 `Son`，在使用 `objc_msgSend` 时，第一个参数是` receiver `也就是 `self`。第二个参数，要先找到 `class` 这个方法的 `selector`，先从  `Son` 这个类开始找，没有，然后到 `Son` 的父类 `Father` 中去找，也没有，再去`Father` 的父类 `NSObject` 去找，一层一层向上找之后，在` NSObject` 的类中发现这个 `class` 方法，而 `NSObject` 的这个` class` 方法，就是返回 `receiver` 的类别，所以这里输出 `Son`。



>当使用 `[super class]` 时，这时要转换成 `objc_msgSendSuper` 的方法。先构造 `objc_super` 的结构体吧，第一个成员变量就是 `self`，第二个成员变量是 `Father`，然后要找 `class` 这个 `selector`，先去` superClass` 也就是 `Father` 中去找，没有，然后去 `Father` 的父类中去找，结果还是在` NSObject `中找到了。然后内部使用函数` objc_msgSend(objc_super->receiver, @selector(class)) ` 去调用，此时已经和我们用 `[self class]` 调用时相同了，此时的 `receiver` 还是 `Son*son`，所以这里返回的也是 `Son`。

