title: Objective C中类方法load和initialize的区别
date: 2016-04-15 
categories: iOS
tags: [Notes]

---

[NSObject Class Reference](https://developer.apple.com/reference/objectivec/nsobject?language=objc)里对这两个方法说明：


<!-- more --> 

```objc
+(void)initialize

    The runtime sends initialize to each class in a program exactly one time just before the class, or any class that inherits from it, is sent its first message from within the program. (Thus the method may never be invoked if the class is not used.) The runtime sends the initialize message to classes in a thread-safe manner. Superclasses receive this message before their subclasses.


+(void)load

    The load message is sent to classes and categories that are both dynamically loaded and statically linked, but only if the newly loaded class or category implements a method that can respond.
    The order of initialization is as follows:

    All initializers in any framework you link to.
    All +load methods in your image.
    All C++ static initializers and C/C++ __attribute__(constructor) functions in your image.
    All initializers in frameworks that link to you.

In addition:

    A class’s +load method is called after all of its superclasses’ +load methods.
    A category +load method is called after the class’s own +load method.

    In a custom implementation of load you can therefore safely message other unrelated classes from the same image, but any load methods implemented by those classes may not have run yet.
```

文档中说明了`initialize`和`load`的区别在于：`load`是只要类所在文件被引用就会被调用，而`initialize`是在类或者其子类的第一个方法被调用前调用（懒加载）。所以如果类没有被引用进项目，就不会有`load`调用；但即使类文件被引用进来，但是没有使用，那么`initialize`也不会被调用。

它们的相同点在于：方法只会被调用一次。（相对`runtime`）。

文档也明确阐述了方法调用的顺序：父类(`Superclass`)的方法优先于子类(`Subclass`)的方法，类中的方法优先于类别(`Category`)中的方法。


