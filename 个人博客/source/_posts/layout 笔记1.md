title: iOS Auto Layout Demystified 学习笔记（一）
date: 2016-02-20 
categories: iOS
tags: [Auto Layout]

---

#### **Auto Layout** 的由来

2012年，Auto Layout 作为`iOS 6`版本的一部分，首次露面。 Auto Layout 的前身是`Cassowary` 约束解析包。

> `Cassowary`是一种递增式约束解析工具包，它能有效的解析线性等式系统和线性不等式系统。约束可能是需求，也可能是偏好。该工具包能够快速的重新解析系统，并且支持UI应用程序。

<!--More-->

#### 使用**Auto Layout** 的好处

+ 建立几何关系
+ 内容驱动布局
+ 优先级规则
+ 检查和模块化
+ 与 Autosizing 兼容

#### 约束

约束是指用来描述视图布局的规则，使用的约束属于 `NSLayouConstra`类。

> **每个约束涉及一两个视图。约束将一个视图的属性与自身关联或者与另一个视图关联**

使用约束可以满足系统的布局要求，建立的规则必须是单独使用和作为整体使用时都有意义。一个视图不能同时位于另一个视图的左边和右边，使用约束时要确保规则严格一致。<br/>
当规则失败时，会引起一连串的反应。在编译时，`Xcode`会发出警告，指出冲突的`IB`约束以及其他基于`IB`的布局问题。在运行时，每当解析程序遇到冲突，`Xcode`控制台都会提供更详细的更新，输出可能的错误原因，并提供调试援助。<br/>
当面对复杂的布局解决方案时，要保证约束的充分性，消除歧义。

##### 约束的属性

+ NSLayoutRelationLessThanOrEqual
+ NSLayoutRelationEqual
+ NSLayoutRelationGreaterThanOrEqual

> 在国际化应用程序时，尽量多使用**前**和**后**，少用 **左**和**右**。这样当使用那个从右到左的语言时，界面可以正确的反转过来。

##### 约束的方法

在此只是简单说一下测试约束正确的几个方法及属性，在以后的学习笔记中会详细说明约束的方法。

```objc
-(BOOL)hasAmbiguousLayout NS_AVAILABLE_IOS(6_0); //用来测试视图的约束是否充分

-(void)exerciseAmbiguityInLayout NS_AVAILABLE_IOS(6_0); //这个方法会随机改变视图的layout到另外一个有效的layout。这样我们就可以很清楚的看到哪一个layout导致了整体的布局约束出现了错误，或者我们应该增加更多的布局约束。

-(CGSize)intrinsicContentSize NS_AVAILABLE_IOS(6_0); //描述数据未经压缩或裁剪的情况下表达视图所需的最小空间，该属性源于每个视图所呈现内容的自然属性

@property(nonatomic) BOOL translatesAutoresizingMaskIntoConstraints NS_AVAILABLE_IOS(6_0); // Default `YES `<br/>
// 指明view使用你在自动布局时给的初始值，还在使用自动布局，忽略默认值。Interface Builder 中会自动设置为NO，但是使用代码时，你需要为所有的视图手动设置。
```

#### 压缩阻力和内容吸附

+ 压缩阻力是指视图保护其内容的方式。压缩阻力高的视图能够抵抗收缩，不允许内容被剪切。<br/>
![](http://7xt3bw.com2.z0.glb.clouddn.com/5.png-water)

+ 内容吸附正好相反，它是防止在视图与其核心内容间作填充或直接伸展其核心内容。<br/>
![](http://7xt3bw.com2.z0.glb.clouddn.com/6.png-water)

+ `IB` 中的设置<br/>
![](http://7xt3bw.com2.z0.glb.clouddn.com/7.png-water)
