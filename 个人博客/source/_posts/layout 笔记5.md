title: iOS Auto Layout Demystified 学习笔记（五）
date: 2016-03-08 
categories: iOS
tags: [Auto Layout]

---

本文将专注并聚焦底层约束。

##### Xcode反馈

Xcode 提供了贯穿界面创建过程的约束反馈，我们将在开发、编译、运行时收重要的状态更新。

##### 调试规则

- 使用自动布局时，不要忘记设置`translatesAutoresizingMaskIntoConstraints`属性。
- 使用约束时，可以尝试调整优先级来解决冲突。
- 存在约束歧义时，要详细阅读日志。
- 注意检查数学逻辑。
- 调试时，必须考虑参与试图的所有约束。约束引用可能存在于任何祖先视图，而位置引用总是存在于祖先视图中。

##### 启动参数

`Xcode`可以添加启动参数，这些参数将作为默认设置传递给你的应用。<br/>
具体打开步骤：

1. 选择`Product|Scheme|Edit Scheme`
2. 选择`Run`模式，打开`Arguments`选项卡
3. 点击`Arguments Passed on Launch | +` 新增一个参数

具体参数列表

|参数|值|效果|
|:---|:----|:----|
|UIViewShowAlignmentRects|Yes|在视图上显示对齐矩形|
|NSDoubleLocalizedStrings|Yes|强制字符串重复，国际化时候要严格测试|

> `iOS`目前支持两套从左到右的语言：阿拉伯语和希伯来语。
