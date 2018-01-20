title: iOS Runtime 关联对象
date: 2016-03-24 
categories: iOS
tags: [Runtime]

---

##### 概述

[Associated Objects](http://nshipster.cn/associated-objects/)(关联对象)与它相关在`<objc/rumtime.h>`中有3个C函数，他们可以让对象在运行时关联任何值：

(1)用给定的`key`和`policy`来为指定对象`(object)`设置关联对象值`(value)`

```c
	void objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy)
```

(2)根据给定的`key`从指定对象`(object)`中获取相对应的关联对象值

```c
	id objc_getAssociatedObject(id object, const void *key)
```

(3)移除指定对象的全部关联对象

```c
	void objc_removeAssociatedObjects(id object)
```

##### 关联对象的特性

在`#import <objc/runtime.h>`中关于`objc_AssociationPolicy`的定义如下：

	enum {
	    OBJC_ASSOCIATION_ASSIGN = 0,
	    OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1,
	    OBJC_ASSOCIATION_COPY_NONATOMIC = 3,
	    OBJC_ASSOCIATION_RETAIN = 01401,
	    OBJC_ASSOCIATION_COPY = 01403
	};
	typedef uintptr_t objc_AssociationPolicy;

`objc_AssociationPolicy`是一个枚举类型的数据结构定义了`OBJC_ASSOCIATION_ASSIGN`、`OBJC_ASSOCIATION_RETAIN_NONATOMIC`、`OBJC_ASSOCIATION_COPY_NONATOMIC`、`OBJC_ASSOCIATION_RETAIN`和`OBJC_ASSOCIATION_COPY`这样五个关联对象特性，每个特性的描述如下：

```c
    OBJC_ASSOCIATION_ASSIGN,给关联对象指定弱引用,相当于@property(assign)或@property(unsafe_unretained)

    OBJC_ASSOCIATION_RETAIN_NONATOMIC,给关联对象指定非原子的强引用,相当于@property(nonatomic,strong)或@property(nonatomic,retain)

    OBJC_ASSOCIATION_COPY_NONATOMIC,给关联对象指定非原子的copy特性,相当于@property(nonatomic,copy)

    OBJC_ASSOCIATION_RETAIN,给关联对象指定原子强引用,相当于@property(atomic,strong)或@property(atomic,retain)

    OBJC_ASSOCIATION_COPY,给关联对象指定原子copy特性,相当于@property(atomic,copy)
```

##### 示例代码

```c
- (void)viewDidLoad {
- 
    [super viewDidLoad];
     
    UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
    [btn setTitle:@"点我" forState:UIControlStateNormal];
    [self.view addSubview:btn];
    [btn setFrame:CGRectMake(50, 50, 50, 50)];
    btn.backgroundColor = [UIColor redColor];
     
    [btn addTarget:self action:@selector(click:) forControlEvents:UIControlEventTouchUpInside];
     
}

-(void)click:(UIButton *)sender
{
    NSString *message = @"你是谁";
     
    UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"提示" message:@"我要传值·" delegate:self cancelButtonTitle:@"确定" otherButtonTitles:nil];
    alert.delegate = self;
    [alert show];
     
    objc_setAssociatedObject(alert, @"msgstr", message,OBJC_ASSOCIATION_ASSIGN);

    //把alert和message字符串关联起来，作为alertview的一部分，关键词就是msgstr，之后可以使用objc_getAssociatedObject从alertview中获取到所关联的对象，便可以访问message或者btn了
     
	//关联传值
    objc_setAssociatedObject(alert, @"btn property",sender,OBJC_ASSOCIATION_ASSIGN);
}

-(void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
{
    //通过 objc_getAssociatedObject获取关联对象
    NSString  *messageString =objc_getAssociatedObject(alertView, @"msgstr");
       
    UIButton *sender = objc_getAssociatedObject(alertView, @"btn property");
    NSLog(@"%ld",buttonIndex);
    NSLog(@"%@",messageString);
    NSLog(@"%@",[[sender titleLabel] text]);
         
    //使用函数objc_removeAssociatedObjects可以断开所有关联。通常情况下不建议使用这个函数，因为他会断开所有关联。只有在需要把对象恢复到“原始状态”的时候才会使用这个函数。
}
 
终端打印：
2015-07-22 16:18:35.294 test[5174:144121] 0
2015-07-22 16:18:35.295 test[5174:144121] 你是谁
2015-07-22 16:18:35.295 test[5174:144121] 点我
```