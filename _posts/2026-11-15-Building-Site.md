---
title: 本网站建站指南-域名篇-2026免费版
date: 2026-11-15 19:20:09 +0800
categories: [webpage]
tags: [HTML,github pages]
description: 曲奇站点迁移全过程记录
toc: true
comments: true
pin: false
math: true
mermaid: true
---

## 获得域名
在我建这个博客时，博客地址是*buttercoookie.github.io*（现在是个人自介页）。偶然的机会我发现了一个现在仍然可以免费注册网址的平台：[digitalplat](https://digitalplat.org/)。它免费注册的后缀是*xxx.dpdns.org*。于是便突发奇想来把自己的博客搬到名字短一点的另一个站点。

首先，我们进入平台的[免费域名页面](https://domain.digitalplat.org/)，注册一个账号并通过2FA绑定github账号之后即可进入[管理后台](https://dash.domain.digitalplat.org/)，点击`Domain Registration`按钮，可以进入域名可用性查询系统。**目前免费域名只有*dpdns.org*，其他都要收费。** 如果*star*了github上的项目链接可以多获得一个域名槽。**每一个域名都会在开始使用360天后失效，失效后180天内可以续订，同时在180天后就可以开始续订。180天后开始续订后就是永久续订了。你可以定一个日历或使用自动操作的插件。**

![注册域名](/Building-Site/1.png)

填写好信息后点击`Check Availability`，如果显示`Sorry, this domain name is already registered`代表域名已经被注册了，请点击`Go Back`重新选择域名。

![域名重复](/Building-Site/2.png)\
![可以使用](/Building-Site/3.png)

进入如果有绿色框框显示`Congratulations! This domain name is now available`代表域名没有被注册，你可以使用。确认完`Your domain information`后还不算注册成功，只是验证了能注册。

接下来是关键步骤，请在此之前**不要关闭原来的网页**，并打开[cloudflare](https://www.cloudflare.com/)，你也可以免费注册一个账号。接下来进入你的`Account home`。

为了添加这个网址，点击`Onboard a domain`，里面的网页仅需输入`Enter an existing domain`然后点击`continue`即可。

![添加网页1](/Building-Site/4.png)\
![添加网页2](/Building-Site/5.png)

此时还没有绑定成功，因为*cloudflare*需要向网页发放平台获取许可。在个人主页里点击链接进入，左侧边栏点击`DNS/Records`进入页面划到最底部`Cloudflare Nameservers`，表格里面就是两对键值对，形如：

| Type | Value |
| :---: | :---: |
| NS | `A` |
| NS | `B` |

回到免费域名平台网页，将`A`和`B`分别填入`Nameserver configuration`中的`Name Server 1`和`Name Server 2`，点击`Register!`后，这个域名就真正属于你了。接下来的步骤也和免费域名平台网页无关了。

## 给予*Github Pages*编辑权限

进入*cloudflare*页面，回到刚才的`DNS`页面。我们已经给*cloudflare*绑定了编辑权限，现在要将编辑权限绑定至*Github Pages*。

请记住以下*Github Pages*的四个IP地址：\
`185.199.108.153`\
`185.199.109.153`\
`185.199.110.153`\
`185.199.111.153`

在`DNS`页面中，找到`DNS management for...`栏目，点击`Add record`开始增加IP配置。

### 主站/www子域
主站和www子域是最常见的主页页面，一般直接绑定*Github Pages*。

对于主站和www子域，我建议你绑定四条`A`型`DNS`，不要用一条`CNAME`（经试验之后*Github Pages*会无法开启`HTTPS`协议）。

对于每一条`DNS`，你需要填写`Type, Name, IPV4 address, Proxy Status, TTL`五个选项。`Type`选择`A`，`Name`代表是哪个子域，主站就填`@`，`www`子域就用`www`，`IPV4 Address`四条不一样、分别对应上面*Github Pages*的四个IP地址，`Proxy Status`建议不开（灰色云朵状态），`TTL`选`Auto`（橙色云朵选不了，两种云朵这个选项都不用动），形式类似于以下：

| Type | Name | Content | Proxy status | TTL |
| :---: | :---: | :---: | :---: | :---: |
| `A` | `@/www` | `185.199.xxx.153` | `DNS only` | `auto` |

一般建议主站和`www`子域都配置一下，总共8条`DNS`。

### 其他子域
使用非*github*后缀的好处之一就是方便添加子域。其他子域建议使用`CNAME`配置，方便且易绑定。

增加一条`CNAME`型`DNS`，只需要在添加时将`Type`字段选择`CNAME`，`Name`字段填子域前缀，特有的`Target`字段填你的`xxx.github.io`链接（没有子域前缀！）即可。剩下的配置方法和刚才一样，形式类似于（以常见的`cdn`子域为例）：

| Type | Name | Content | Proxy status | TTL |
| :---: | :---: | :---: | :---: | :---: |
| `CNAME` | `cdn` | `xxx.github.io` | `DNS only` | `auto` |

![绑定DNS](/Building-Site/7.png)

## *Github Pages*绑定页面至域
完成了关键步骤，加下来应该让*github*往域名里加页面了。进入*github*中想要绑定网页的仓库，进入`Settings`标签页，左侧侧边栏选择`Pages`页面。请确保你的页面已经通过`Github Actions`或`Deploy from a branch`发布成功！

接下来在`Custom domain`这一栏写下你这个仓库的绑定链接（是子域这里要加上子域前缀），然后`Save`即可。

接下来会进行`DNS`验证。这里验证不通过是没有关系的，曲奇就是主站和`www`子域配置通不过验证（亲测`CNAME`类型的`DNS`也通不过）、在`cdn`等子域都能通过，但所有页面都正常、成功上线。勾选下面的`Enforce HTTPS`会让你的网页变成`HTTPS`协议。

![github上线](/Building-Site/8.png)

## 结语
绑定页面是一个需要很细心的复杂过程，如有疑问或错误要指出，请在评论区留言。

最后，祝大家新年快乐啦！