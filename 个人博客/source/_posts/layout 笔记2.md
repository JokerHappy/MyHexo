title: iOS Auto Layout Demystified 学习笔记（二）
date: 2016-02-28 
categories: iOS
tags: [Auto Layout]

---

`Auto Layout`是一种约束满足系统。所谓**约束**其本质是限制的意思。你创建的每个规则都给出了一个要求，这个要求规定了界面的一部分和另一部分的关系。这些规则可以用数字标识的优先级进行排序，`Auto Layout`根据你定制的规则和优先级序号确定界面的**可视化**呈现方式。
<br/>
<!--More-->
#### 约束的类型

对于Autolayout而言，最为核心的基础就是约束。主要分为以下几种:

- **NSLayoutConstraint**，开放类，几乎是程序员最常用的约束。它用于设置`view`在`view tree`之间的关系，自身大小等。

- **NSContentSizeLayoutConstraint**，私有类。用于衡量`view`内容和大小相关的约束。比如`Hugging`和`Compression`，控制view的内容显示。

- **NSAutoresizingMaskLayoutConstraint**，私有类。由`Autosizing mask`转换到`Autolayout`系统中的约束表达。

- **_UILayoutSupportConstraint**，私有类。布局支撑约束，它包括`Top`和`Bottom`的约束，用于控制view的显示边界，例如，它限制view的顶端显示不会和状态栏重合。

- **NSIBPrototypingLayoutConstraint**，私有类。如果你在`Storyboard`中添加了一个UI控件，且没有在Storyboard中添加任何约束，但是标注了你要使用`Autolayout`，那么在实际的运行期，系统会默认为它添加`NSIBPrototypingLayoutConstraint`约束。

值得一提的是：`NSContentSizeLayoutConstraint`最主要的作用是和`intrinsic size`一起工作，通常这个约束和Layout约束共同决定view的显示方式。

>所有的约束类型都具有以下特性： 

> + 都表达了视图在屏幕上的布局方式。
> + 都有一个内在的优先级，它指定了每个请求在`Auto Layout`系统中的强烈程度。

#### 优先级

它通常是形容一个约束重要性的指标，通常两个约束如果共同决定一个显示属性的显示，当他们发生了抵触的时候，优先级表示其中一个约束（优先级较高），相比另外一个而言，更加重要。

#### 内容大小约束

+ 内容吸附
+ 压缩阻力

##### 通过代码设置内容大小约束

- 内容吸附优先级默认值为**250**
- 压缩阻力优先级默认值为**750**

```c 
-(UILayoutPriority)contentHuggingPriorityForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);

-(void)setContentHuggingPriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);

-(UILayoutPriority)contentCompressionResistancePriorityForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);

-(void)setContentCompressionResistancePriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);

typedef NS_ENUM(NSInteger, UILayoutConstraintAxis) {
    UILayoutConstraintAxisHorizontal = 0,
    UILayoutConstraintAxisVertical = 1
};
```

##### 通过IB设置

详见 [**笔记（一）**](http://jokerhappy.github.io/post/layout%20笔记1/);

#### 构建约束布局

创建布局约束的方法有以下三种：

- 利用`StoryBoard`或`IB`设计界面。

- 用可视化格式语言描述约束，并允许`NSLayoutConstraint`类根据你的请求生成具体的实例。

- 为每个组件提供一个基本关系，从而构建`NSLayoutConstraint`类的实例。

**具体使用方法会在以后的笔记中详细说明**

##### 构建`NSLayoutConstraint`实例

利用`NSLayoutConstraint`类方法创建实例

```c 
+(instancetype)constraintWithItem:(id)view1 attribute:(NSLayoutAttribute)attr1 relatedBy:(NSLayoutRelation)relation toItem:(nullable id)view2 attribute:(NSLayoutAttribute)attr2 multiplier:(CGFloat)multiplier constant:(CGFloat)c;
```

方法中的变量解释：

- `view1` 与 `view2` ，约束关联的视图。
- `attr1` 与 `attr2` ：约束系统中的名词，描述视图对齐矩阵的特征，如左边、右边、中心、高度等，不存在第二项，使用 `NSLayoutAttributeNotAnAttribute` 占位符。
- `relation`：动词。指出属性之间如何比较。
- `mutiplier` 和 `constant`：提供代数元素，增强约束的功能和灵活性，通过这个属性，可以指出一个视图是另一个视图的一半，也可以指定一个视图是通过另一个视图偏移某个距离得到的。即下式中`m`和`b`。

约束本质的就是数学中的相等或者不等关系，用公式表示就是：

	y （关系）m * x + b

#### 约束、层次结构与边界系统

> 约束引用两个视图时，这两个视图必须在同一个视图层次结构，这个特别重要。

> 对于两个涉及视图的约束，只有两种情况，一种是父视图与子视图，另一种就是视图是平级的，即它们拥有相同的祖先视图。

#### 安装约束

约束创建完成之后，还只是 `NSLayoutConstraint` 类的实例，如果想要约束生效，还需要将约束添加到 `AutoLayout` 系统，例如：

	[myView addConstraint: aConstraintInstance];
	
因为可视化格式系统返回的是约束实例数组，所以还提供另一种方式：

	[myView addConstraints: myArrayConstraints];

> 约束通常安装在该约束所引用视图的最近父视图中。约束有必要安装在引用每个视图的公共的父视图中。约束中的数字相对于所安装视图的坐标系要有意义。<br/>
> **视图被视为自身的父视图**

#### 布局约束法则

- 布局约束具有优先级
- 布局约束没有天然顺序，所有具有相同优先级的约束都被同时考虑
- 布局约束是关系，没有方向
- 布局约束可以取近似值
- 布局约束可以循环
- 布局约束可以冗余（只要不发生冲突）
- 布局约束可以引用兄弟视图
- AutoLayout 对变形的处理可能不是很好
- AutoLayout 支持动画效果
- 布局约束不应在不同的边界系统之间交叉使用
- 布局约束会在运行时失败
- 格式不正确的约束会 crash
- 约束至少引用一个视图
- 小心无效的属性结对



