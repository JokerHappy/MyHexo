title: iOS中AutoLayer自动布局流程及相关方法
date: 2016-02-18 
categories: iOS
tags: [Auto Layout]

---

`Auto Layout`  重新构思了开发者创建界面的方式。它创建了一个灵活、强大的系统，来描述试图和他们的内容是如何相互关联的，他们和他们占据的窗口和父试图是如何关联的。

<!-- more-->
#### Auto Layout Process 自动布局过程

`Auto Layout`在`view`显示之前，主要有三个步骤`updating constraints -> layout -> display`。每一个步骤都依赖于上一个。`display`依赖`layout`，而`layout`依赖`updating constraints`。 

+ `updating constraints`，被称为测量阶段，其从下向上(from subview to super view),为下一步`layout`准备信息。可以通过调用方法`setNeedUpdateConstraints`去触发此步。`constraints`的改变也会自动的触发此步。但是，当你自定义`view`的时候，如果一些改变可能会影响到布局的时候，通常需要自己去通知`Auto Layout`->`updateConstraintsIfNeeded`。
自定义`view`的话，通常可以重写`updateConstraints`方法，在其中可以添加`view`需要的局部的`contraints`。

+ `layout`，其从上向下(from super view to subview)，此步主要应用上一步的信息去设置`view`的`center`和`bounds`。可以通过调用`setNeedsLayout`去触发此步骤，此方法不会立即应用`layout`。如果想要系统立即的更新`layout`，可以调用`layoutIfNeeded`。另外，自定义`view`可以重写方法`layoutSubViews`来在`layout`的工程中得到更多的定制化效果。

+ `display`，此步时把`view`渲染到屏幕上，它与你是否使用`Auto layout`无关，其操作是从上向下(from super view to subview)，通过调用`setNeedsDisplay`触发，因为每一步都依赖前一步，因此一个`display`可能会触发`layout`，当有任何`layout`没有被处理的时候，同理，`layout`可能会触发`updating constraints`，当`constraint system`更新改变的时候。


![](http://7xt3bw.com1.z0.glb.clouddn.com/4.png)

#### iOS 提供的Layout 的方法

##### layoutSubviews

- 在`iOS5.1`和之前的版本，此方法的缺省实现不会做任何事情(实现为空)，`iOS5.1`之后(iOS6开始)的版本，此方法的缺省实现是使用你设置在此view上面的constraints(Autolayout)去决定subviews的`position`和`size`。 UIView的子类如果需要对其`subviews`进行更精确的布局，则可以重写此方法。只有在autoresizing和constraint-based behaviors of subviews不能提供我们想要的布局结果的时候，我们才应该重写此方法。可以在此方法中直接设置subviews的`frame`。 我们不应该直接调用此方法，而应当用下面两个方法。

##### setNeedsLayout

- 此方法会将view当前的layout设置为无效的，并在下一个`upadte cycle`里去触发`layout`更新。

##### layoutIfNeeded

- 使用此方法`强制立即`进行layout,从当前view开始，此方法会遍历整个view层次(包括superviews)请求layout。因此，调用此方法会强制整个view层次布局。

#### 基于AutoLayer约束constraints的方法：

##### setNeedsUpdateConstraints

- 当一个自定义view的某个属性发生改变，并且可能影响到constraint时，需要调用此方法去标记constraints需要在未来的某个点更新，系统然后调用`updateConstraints`.

##### needsUpdateConstraints

- `constraint-based layout system`使用此返回值去决定是否需要调用`updateConstraint`s作为正常布局过程的一部分。

##### updateConstraintsIfNeeded

- 立即触发约束更新，自动更新布局。

##### updateConstraints

- 自定义view应该重写此方法在其中建立constraints. 注意：要在实现在最后调用`[super updateConstraints]`














