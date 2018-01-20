title: iOS Auto Layout Demystified 学习笔记（三）
date: 2016-03-02 
categories: iOS
tags: [Auto Layout]

---

本文主要探究`Interface Builder`自动布局。<br/>

##### 在IB中使用/禁用自动布局

在右侧面板中勾选`Use Autolayout`选项，不做赘述。<br/>如果想在代码中退出`Use Autolayout`需要设置视图的属性`translatesAutoresizingMaskIntoConstraints`属性为`YES`。

##### 基本布局及自动生成的约束

当你在`IB`中放置好视图时，在不添加约束的情况下，`Xcode`会**自动**帮助你免费添加一些根据视图的框架构建的约束，为界面元素提供最小的回退行为。

##### `IB`元素指南

具体元素在此不再赘述，需要开发者多多使用。<br/>
使用的小窍门

1. 按下`Ctrl`键从一个视图拖拽到另一个视图，在弹出的选择列表时，在选择约束类型是按住`Shift`可以进行多选.
2. 在`Intrinsic Size`中的选项`Placeholder`为设计中的视图提供一个占位空间，在程序运行时在具体计算视图的尺寸。
3. 在`IB`中，同时按下`Ctrl`+`Shift`键并单击任意区域，会显示该位置所放置的所有视图的清单，在复杂的布局中可以快速选择所要操作的视图。

##### 添加约束

略

##### 预览布局

在IB中布局完成后，不需要每次都运行程序查看布局效果，在IB中提供了简单的方法，可以展示在不同设备上的布局效果包括横竖屏。具体步骤：

+ 首先需要关闭多余的视图，腾出更多的空间。
+ 选择`IB`左上角，如下图所示<br/>
![](http://7xt3bw.com1.z0.glb.clouddn.com/8.png-water)
<br/>
+ 按住`Shift`和`Option`键，选择对应的IB视图，会出现如下视图<br/>
![](http://7xt3bw.com1.z0.glb.clouddn.com/9.png-water)
<br/>
+ 选择右侧下方**＋**,双击或者回车，就会出现对应的预览视图<br/>
![](http://7xt3bw.com1.z0.glb.clouddn.com/10.png-water)
<br/>
+ 最后就可以选择各种尺寸的屏幕，进行预览。

在`IB`中还可以为视化编辑器添加一些视图覆盖物，用于测试，选中控件后再选择菜单中<br/>`Editor->Canvas`，可以选择测试效果。

> 小窍门<br/>在应项目的`Edit Scheme`中设置一个启动参数 `UIViewShowAlignmentRects` 并将参数值设置为`YES`，可以让程序在运行时显示视图的对齐矩阵（alignment rectangle）。-
	UIViewShowAlignmentRects YES
