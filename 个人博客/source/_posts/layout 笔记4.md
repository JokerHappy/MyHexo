title: iOS Auto Layout Demystified 学习笔记（四）
date: 2016-03-05 
categories: iOS
tags: [Auto Layout]

---

本文主要探究利用可视化格式语言进行自动布局。

##### 可视化格式约束介绍

可视化格式由一个描述视图布局的文本字符串组成。你可以根据视图在界面中出现的顺序列出他们，文本中可以指定间隔、不等量及优先级，可以使布局形象化地表现为一个简单的文本。
如下所示

```objc
[self.view addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"V:|-[view1](==200)-(>=20@750)-[view2]" options:NSLayoutFormatAlignAllLeft metrics:nil views:NSDictionaryOfVariableBindings(view1,view2)]];
```

其中`NSDictionaryOfVariableBindings()`表示：当使用可视化约束时，布局系统将名称字符串同它们表示的对象关联起来，利用此宏创建一个约束列表（字典）。
在代码中`NSDictionaryOfVariableBindings(view1,view2)`相当于创建了下列字典

	@{@"view1":view1,@"view2":view2}

当然你可以自定义字典，将视图进行关联。
代码中其他符号的含义

| 变量 | 释义 |
| :--- | :--- |
| V:，H: |	方向 |
| metric |	度量 |
| == <= >= | 关系 |
| @ |	优先级 |
| ｜ |	父视图 |
| options | 对齐方式 |
| - | 标准间隔：视图到视图为8点；视图到父视图为20点 |

- 其中8点和20点的值来源于苹果的`Aqu`a用户界面标准，作为基本的视觉主题应用于`OS X`设计
- 如果间隔为负数的时候，需要使用`()`
- 可视化格式可以加入多个视图
- 视图的`()`中指定视图的尺寸，根据排列方向对应固定的宽度或高度
- 使用可视化格式创建的所有约束都有一个默认的优先级，即1000（必需的）

##### NSLog 和可视化格式

在`NSLayoutConstrain`t日志中任何可能的地方`Xcode`都添加了可视化格式表示，即使完全适用`IB`或者通过单独的约束实例来创建约束，通过学习这种布局语言，你也会受益匪浅。
实践是检验真理的唯一标准，多去尝试吧。