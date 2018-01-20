title: UIWebView 使用浅析
date: 2016-02-16 
categories: iOS  
tags: Knowledge

---

苹果官方对`UIWebView`的介绍

> You can use the UIWebView class to embed web content in your application. To doso, you simply create a UIWebView object, attach it to a window, and send it a request to load web content. You can also use this class to move back and forward in the history of webpages, and you can even set some web content properties programmatically.

`UIWebView`是`iOS`内置的浏览器控件，`UIWebView`用于在`App`中嵌入网页，通常是`html`网页，也可以是`PDF`、`txt`文档等。苹果在发布`iOS8`的时候，新增了一个`WKWebView`组件，如果你的`APP`只考虑支持`iOS8`及以上版本，那么你就可以使用这个新的浏览器控件了。

<!-- more --> 
本文只是探讨`UIWebView`的使用方法。

#### 属性

`@property (nonatomic) BOOL scalesPageToFit;` 
> 自动适应屏幕尺寸

`@property (nonatomic) BOOL detectsPhoneNumbers NS_DEPRECATED_IOS(2_0, 3_0);` 
> 自动检测网页上的电话号码，单击可以拨打，`iOS4`之后被下面的属性所代替。

`@property (nonatomic) UIDataDetectorTypes dataDetectorTypes NS_AVAILABLE_IOS(3_0);` 
> 自动检测电话、网址和邮箱。枚举见下方代码。

```c
typedef NS_OPTIONS(NSUInteger, UIDataDetectorTypes) {
	UIDataDetectorTypePhoneNumber                              = 1 << 0,          // Phone number detection
	UIDataDetectorTypeLink                                     = 1 << 1,          // URL detection
	UIDataDetectorTypeAddress NS_ENUM_AVAILABLE_IOS(4_0)       = 1 << 2,          // Street address detection
	UIDataDetectorTypeCalendarEvent NS_ENUM_AVAILABLE_IOS(4_0) = 1 << 3,          // Event detection
	UIDataDetectorTypeNone          = 0,               // No detection at all
	UIDataDetectorTypeAll           = NSUIntegerMax    // All types
} __TVOS_PROHIBITED;
```

`@property (nonatomic) BOOL allowsInlineMediaPlayback NS_AVAILABLE_IOS(4_0);` 
`// iPhone Safari defaults to NO. iPad Safari defaults to YES`  
> `UIWebView`中用`html5`的`video`方式播放视频时，在`iPad`上是默认原来大小的，而在`iPhone`上是默认全屏播放的。
**`HTML`里`video`必须加上`webkit-playsinline`属性**

`@property (nonatomic) BOOL mediaPlaybackRequiresUserAction NS_AVAILABLE_IOS(4_0);` 
`// iPhone and iPad Safari both default to YES`
> 是否可以自动播放`html5`中的视频。

`@property (nonatomic) BOOL mediaPlaybackAllowsAirPlay NS_AVAILABLE_IOS(5_0);`
`// iPhone and iPad Safari both default to YES`
> 是否可以使用`Air Play`。 在`iPhone`和`iPad`上默认使`YES`。

`@property (nonatomic) BOOL suppressesIncrementalRendering NS_AVAILABLE_IOS(6_0);` 
`// iPhone and iPad Safari both default to NO`
> 是否网页内容下载完毕才开始渲染`web`视图，默认为`NO`。

`@property (nonatomic) BOOL keyboardDisplayRequiresUserAction NS_AVAILABLE_IOS(6_0); // default is YES`
> 是否在`web`页面响应用户输入弹出键盘，默认为`YES`。

*`iOS7`增加了分页功能*
`@property (nonatomic) UIWebPaginationMode paginationMode NS_AVAILABLE_IOS(7_0);`
> 设置分页模式

```c
typedef NS_ENUM(NSInteger, UIWebPaginationMode) {
	UIWebPaginationModeUnpaginated,//不使用翻页效果
	UIWebPaginationModeLeftToRight,//将网页超出部分分页，从左向右进行翻页
	UIWebPaginationModeTopToBottom,//将网页超出部分分页，从上向下进行翻页
	UIWebPaginationModeBottomToTop,//将网页超出部分分页，从下向上进行翻页
	UIWebPaginationModeRightToLeft//将网页超出部分分页，从右向左进行翻页
};
```

`@property (nonatomic) UIWebPaginationBreakingMode paginationBreakingMode NS_AVAILABLE_IOS(7_0);`
> 设置分页打破模式
 
```c
typedef NS_ENUM (NSInteger, UIWebPaginationBreakingMode ) {
	UIWebPaginationBreakingModePage, // 页模式
	UIWebPaginationBreakingModeColumn //列模式 
};
```

`@property (nonatomic) CGFloat pageLength NS_AVAILABLE_IOS(7_0);`
> 每一页的长度

`@property (nonatomic) CGFloat gapBetweenPages NS_AVAILABLE_IOS(7_0);`
> 每一页的间距

`@property (nonatomic) BOOL allowsPictureInPictureMediaPlayback NS_AVAILABLE_IOS(9_0);`
> 画中画模式页面中返回 **`iOS9`新增属性**

`@property (nonatomic) BOOL allowsLinkPreview NS_AVAILABLE_IOS(9_0); // default is NO`
> 使用`3DTouch`显示链接预览 **`iOS9`新增属性**

#### 方法

```c
	- (void)reload; //重新加载（刷新）
	- (void)stopLoading; //停止加载
	- (void)goBack;//回退
	- (void)goForward; //前进
	- (void)loadRequest:(NSURLRequest *)request;
	- (void)loadHTMLString:(NSString *)string baseURL:(nullable NSURL *)baseURL;
	- (void)loadData:(NSData *)data MIMEType:(NSString *)MIMEType textEncodingName:(NSString *)textEncodingName baseURL:(NSURL *)baseURL;
```

#### 代理方法

```c
	- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType;
	- (void)webViewDidStartLoad:(UIWebView *)webView;
	- (void)webViewDidFinishLoad:(UIWebView *)webView;
	- (void)webView:(UIWebView *)webView didFailLoadWithError:(nullable NSError *)error;
```

#### UIWebView 的高级用法

##### 隐藏上下滚动时出边界的后面的黑色的阴影

```c
for (UIView *_aView in [webView subviews]){
  
	if ([_aView isKindOfClass:[UIScrollView class]]){

		[(UIScrollView *)_aView setShowsVerticalScrollIndicator:NO];
		//右侧的滚动条  
		[(UIScrollView *)_aView setShowsHorizontalScrollIndicator:NO]; 
		//下侧的滚动条   
		[(UIScrollView *)_aView setAlwaysBounceHorizontal:NO];//禁止左右滑动
		
		for (UIView *_inScrollview in _aView.subviews){
			if ([_inScrollview isKindOfClass:[UIImageView class]]){

				_inScrollview.hidden = YES;  //上下滚动出边界时的黑色的图片
			}  
		}  
	}  
}  
```

##### 禁用拖拽时的反弹效果

	[(UIScrollView *)[[webView subviews] objectAtIndex:0] setBounces:NO];

##### 判断用户点击类型

```c
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    switch (navigationType) {
		//点击连接
        case UIWebViewNavigationTypeLinkClicked:
        {
       	    NSLog(@"clicked");
        }
        break;
       	//提交表单
        case UIWebViewNavigationTypeFormSubmitted:
        {
           	NSLog(@"submitted");
        }
        default:
        break;
    	}
    return YES;
}
```
  
##### `UIWebView`和`JavaScript`的交互

###### Objective-C调用JavaScript

用`Objective-C`调用`JavaScript`会相对简单些，在`UIWebView`加载完成之后调用`UIWebView`的`stringByEvaluatingJavaScriptFromString:`方法就行了。参数中的`NSString`字符串就是`js`的代码，可以是`js`函数，也可以是`js`代码。如下所示:

`[myWebView stringByEvaluatingJavaScriptFromString:@"javascriptFunction()"];`

###### Javascript调用Objective-C的函数

通过`UIWebView`的重定向，通过在`UIWebView`的代理函数`webView:shouldStartLoadWithRequest:navigationType:`来监听`URL`的变化，这个函数会在`webview`加载`URL`时回调，我们可以`return YES`来让`webview`继续加载内容，`return NO`来停止继续加载新的内容。事先可以约定好几个虚假的`URL`(比如`tool://goToHomePage`)，我们拿到定义好的`URL`之后做对应的`Objective-C`函数调用。在`iOS7`中会出现代理方法无法调用的情况，具体原因不明。

**实例** 

比如我们在`javascript`文件里添加点击响应的函数

```javascript
function clickButton() {

  	window.location = "tool://goToHomePage"
}
```

然后在`webview`的代理函数中监听，如果是事先定义好的虚假`URL`，就进行对应的处理

```c
-(BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSURL *url = request.URL;

    if ([[url scheme] isEqualToString:@"tool"] && [[url host] isEqualToString:@"goToHomePage"]) {
       //添加跳转到HomePage的逻辑
        [self jumpToHomePage];
        return NO;
    }
    return YES;
}
```

##### UIWebView 捕获 404， 403

```c

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    static BOOL isRequestWeb = YES;

    if (isRequestWeb) {
        NSHTTPURLResponse *response = nil;

        NSData *data = [NSURLConnection sendSynchronousRequest:request   returningResponse:&response error:nil];

        if (response.statusCode == 404) {
            // code for 404
            return NO;
        } else if (response.statusCode == 403) {
            // code for 403
            return NO;
        }

    	[webView loadData:data MIMEType:@"text/html" textEncodingName:nil baseURL:[request URL]];

    	isRequestWeb = NO;

    	return NO;
    	}

    return YES;
}
```