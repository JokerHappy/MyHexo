title: iOS 9 Universal Links
date: 2016-04-10 
categories: iOS
tags: [Notes]

---

`Apple` 推出`Universal Links` (通用链接)：一种能够方便的通过传统 `HTTP` 链接来启动 `APP`, 使用相同的网址打开网站和 `APP`。

通过唯一的网址, 不需要特别的`schema`就可以链接一个特定的视图到`APP` 里面 。比如：在知乎中使用了通用链接, 那么用户在`Safari`、`UIWebView`或者 `WKWebView`点击一个链接, `iOS`设备上的知乎`app`会在知乎里面自动打开这个页面, 如果没有安装则在`Safrai`中打开响应链接。

<!-- more --> 

##### Step 1

创建一个`json` 格式的`apple-app-site-associatio` 文件<br/>
例如：


    {
        "applinks": {
            "apps": [],
            "details": [
                {
                    "appID": "9JA89QQLNQ.com.apple.wwdc",
                    "paths": [ "/wwdc/news/", "/videos/wwdc/2015/*"]
                },
                {
                    "appID": "ABCD1234.com.apple.wwdc",
                    "paths": [ "*" ]
                }
            ]
        }
    }

- `appID` 构成 **TeamID（你的团队标识的）.bundle id**
- `paths` 键设定允许的路径列表, 或只是一个星号如果你想打开 `APP` 而不管路径是什么(**注意区分大小写**)

##### Step 2

上传 `apple-app-site-association` 文件

注意：

- 上传到`.well-known`目录下 /.well-known/apple-app-site-association

- `web server` 需要支持`https`，客户端需要通告`https`访问，并且不支持任何重定向。

##### Step 3

在 APP 里处理通用链接

必须在 `Xcode` 的 `capabilities` 里 添加你的 `APP` 域名, 必须用 `applinks:` 前置它：还添加一些你可能拥有的子域和扩展。

这将使`APP`从域名请求`Step1`中创建的`JSON` 文件 `apple-app-site-association`。当你第一次启动 `APP`，它会从 `web server`根目录下载这个文件。

##### Step 4

在 `AppDelegate` 里支持通用链接

```objc
-(BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray *restorableObjects))restorationHandler{

 	if ([[userActivity activityType] isEqualToString:NSUserActivityTypeBrowsingWeb]) 
    {
        NSString *urlStr = userActivity.webpageURL;
     	
		if ([urlStr.host isEqualToString:@"myapp.com"]){

			// 打开App对应页面
		}else{
			
			// 不能识别，在浏览器中打开
		}
	}
	return YES;
}
```

当 `userActivity` 是 `NSUserActivityTypeBrowsingWeb` 类型, 则意味着它已经由通用链接 `API` 代理。这样的话, 它保证用户打开的 `URL` 将有一个非空的 `webpageURL` 属性。
<br/>

##### [官方文档](https://developer.apple.com/library/ios/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW2)

##### [一些注意要点](http://www.jianshu.com/p/c2ca5b5f391f)



