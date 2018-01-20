title: KeyChain
date: 2016-04-12 
categories: iOS
tags: [Notes]

---

通常情况下，我们用`NSUserDefaults`存储数据信息，但是对于一些私密信息，比如密码、证书等等，就需要使用更为安全的`keychain`了。`keychain`里保存的信息不会因`App`被删除而丢失，在用户重新安装`App`后依然有效，数据还在。

[iOS KeyChain 基础](https://cnbin.github.io/blog/2015/08/18/ios-keychain-ji-chu/)

[KeyChain使用与共享数据](http://blog.csdn.net/ibcker/article/details/24839143)