title: iOS Runtime 消息
date: 2016-03-20 
categories: iOS
tags: [Runtime]

---

`OC`中方法调用在运行时的过程

1. 首先，在相应操作的对象中的缓存方法列表中找调用的方法，如果找到，转向相应实现并执行。

2. 如果没找到，在相应操作的对象中的方法列表中找调用的方法，如果找到，转向相应实现执行

3. 如果没找到，去父类指针所指向的对象中执行1，2.

4. 以此类推，如果一直到根类还没找到，转向拦截调用。

5. 如果没有重写拦截调用的方法，程序报错。

##### 拦截调用

在方法调用中说到了，如果没有找到方法就会转向拦截调用。

拦截调用就是，在找不到调用的方法程序崩溃之前，你有机会通过重写`NSObject`的四个方法来处理。

	+ (BOOL)resolveClassMethod:(SEL)sel;
	+ (BOOL)resolveInstanceMethod:(SEL)sel;
	//后两个方法需要转发到其他的类处理
	- (id)forwardingTargetForSelector:(SEL)aSelector;
	- (void)forwardInvocation:(NSInvocation *)anInvocation;

* 第一个方法是当你调用一个不存在的类方法的时候，会调用这个方法，默认返回`NO`，你可以加上自己的处理然后返回`YES`。

* 第二个方法和第一个方法相似，只不过处理的是实例方法。

* 第三个方法是将你调用的不存在的方法重定向到一个其他声明了这个方法的类，只需要你返回一个有这个方法的`target`。

* 第四个方法是将你调用的不存在的方法打包成`NSInvocation`传给你。做完你自己的处理后，调用`invokeWithTarget:`方法让某个`target`触发这个方法。

具体流程如下：
![](http://7xt3bw.com1.z0.glb.clouddn.com/runtimemethod.png)

具体事例可参考[理解 Objective-C Runtime](http://justinyan.me/post/1624)

##### 动态添加方法

重写了拦截调用的方法并且返回了`YES`，我们要怎么处理呢？

有一个办法是根据传进来的`SEL`类型的`selector`动态添加一个方法。

首先从外部隐式调用一个不存在的方法：

	//隐式调用方法
	[target performSelector:@selector(resolveAdd:) withObject:@"test"];

然后，在`target`对象内部重写拦截调用的方法，动态添加方法。

```c
	void runAddMethod(id self, SEL _cmd, NSString *string){

	    NSLog(@"add C IMP ", string);
	}

	+ (BOOL)resolveInstanceMethod:(SEL)sel{
	    //给本类动态添加一个方法
	    if ([NSStringFromSelector(sel) isEqualToString:@"resolveAdd:"]) {
	        class_addMethod(self, sel, (IMP)runAddMethod, "v@:*");
	    }
	    return YES;
	}
```

其中`class_addMethod`的四个参数分别是：

- `Class cls` 给哪个类添加方法，本例中是`self`

- `SEL name` 添加的方法，本例中是重写的拦截调用传进来的`selector`

-`IMP imp`方法的实现，C方法的方法实现可以直接获得。如果是OC方法，可以用

	+ (IMP)instanceMethodForSelector:(SEL)aSelector

- `"v@:*"`方法的签名，代表有一个参数的方法

##### 方法交换

方法交换，顾名思义，就是将两个方法的实现交换。例如，将A方法和B方法交换，调用A方法的时候，就会执行B方法中的代码，反之亦然。

话不多说，这是参考Mattt大神在NSHipster上的文章自己写的代码。

```c
#import "UIViewController+swizzling.h"
#import @implementation UIViewController (swizzling)
//load方法会在类第一次加载的时候被调用
//调用的时间比较靠前，适合在这个方法里做方法交换

	+ (void)load{
	    //方法交换应该被保证，在程序中只会执行一次
	    static dispatch_once_t onceToken;
	    dispatch_once(&onceToken, ^{
	        //获得viewController的生命周期方法的selector
	        SEL systemSel = @selector(viewWillAppear:);
	        //自己实现的将要被交换的方法的selector
	        SEL swizzSel = @selector(swiz_viewWillAppear:);
	        //两个方法的Method
	        Method systemMethod = class_getInstanceMethod([self class], systemSel);
	        Method swizzMethod = class_getInstanceMethod([self class], swizzSel);
	        //首先动态添加方法，实现是被交换的方法，返回值表示添加成功还是失败
	        BOOL isAdd = class_addMethod(self, systemSel, method_getImplementation(swizzMethod), method_getTypeEncoding(swizzMethod));
	        if (isAdd) {
	            //如果成功，说明类中不存在这个方法的实现
	            //将被交换方法的实现替换到这个并不存在的实现
	            class_replaceMethod(self, swizzSel, method_getImplementation(systemMethod), method_getTypeEncoding(systemMethod));
	        }else{
	            //否则，交换两个方法的实现
	            method_exchangeImplementations(systemMethod, swizzMethod);
	        }
	    });
	}
	- (void)swiz_viewWillAppear:(BOOL)animated{
	    //这时候调用自己，看起来像是死循环
	    //但是其实自己的实现已经被替换了
	    [self swiz_viewWillAppear:animated];
	    NSLog(@"swizzle");
	}
	@end
```

在一个自己定义的viewController中重写viewWillAppear
	
	- (void)viewWillAppear:(BOOL)animated{
	    [super viewWillAppear:animated];
	    NSLog(@"viewWillAppear");
	}
