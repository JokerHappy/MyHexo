---
title: SSL证书类型的选择
date: 2018-02-01 
categories: 
tags: [Notes] 

---

#### SSL证书的类型

通常来说，SSL证书分为四大类 

>DV证书

>OV证书

>EV证书

>自签名证书——自签名证书很少被部署到正式的网站上，一般是被用在内部的测试环境中

<!--more-->

##### DV证书

DV证书即`Domain Validation Certificate`。

DV证书适合个人网站使用，申请证书时，CA只验证域名信息，即whois信息中的管理员邮箱验证。

##### OV证书

OV证书即`Organization Validation CErtificate`。

OV证书是在DV证书的基础上，增加认证公司的信息。

##### EV证书

EV证书即`Extended Validation Certificate`

EV证书的认证最为严格，一般会要求提供纸质材料。

除了认证等级和显示图标的区别，三类证书在域名的支持性、价格、赔偿费用上也是不相同的。 

| 证书类型 | 价格 | 保额 | 单域名 | 多域名 | 泛域名 | 多泛域名 |
|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| DV证书 | 便宜或免费 | 低 |支持 |支持 |不支持 |不支持 |
| OV证书 | 较高 | 中 |支持 |支持 |支持 |支持 |
| EV证书 | 高 | 高 |支持 |支持 |不支持 |不支持 |

总结下：

如果是个人网站，你可以选择DV证书； 

如果是企业网站，但刚刚起步，不想付费，可以选择DV证书； 

如果是综合性的企业门户网站，可以选择OV证书； 

如果是金融类的企业网站，域名需求没有那么多，可以选择EV证书。 

#### SSL厂商

SSL证书的厂商非常多，也没有绝对的好与坏之分。不过我们也可以从行业内影响力、历史安全问题、市场占有率等角度来评判。 

这是今年2月份，w3techs.com发布的SSL证书市场份额报告中的截图。

<image height="500px" src="http://7xt3bw.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-02-05%20%E4%B8%8B%E5%8D%884.52.17.png"></image>

[Godaddy](https://sg.godaddy.com/zh?isc=gennbacn07&currencytype=CNY&utm_source=baidu&utm_medium=cpc&utm_campaign=zh-cn_corp_sem_base_brand_gd&utm_content=GD%20Core%20-%20Phrase&utm_term=godaddy&mkwid=1edNUgIP8_pcrid_18782177220_pdv_c_)的OV证书从购买到部署到使用到安全性，都是比较简便且可信赖的。

