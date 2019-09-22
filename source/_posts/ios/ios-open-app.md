---
author: wanghch
date: 2013-10-24
title: ios 打开其他应用
tags: ios
category: ios
---


[[UIApplication sharedApplication] openURL:[NSURL URLWithString:appString]];

## 1、打开系统应用

打电话：[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"tel://123456"]];

打开浏览器：[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"http://devhc.com"]];

其他url格式如下表所示：

<!--more-->
<h2><a title="Apple URL Scheme Reference" href="https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007899-CH1-SW1">Apple URL Scheme Reference</a></h2>
<table class="table" border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<th scope="col">Application</th>
<th scope="col">URI Scheme or Protocol</th>
<th scope="col">Query Strings</th>
</tr>
<tr>
<td rowspan="4" valign="top"><a href="https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MailLinks/MailLinks.html#//apple_ref/doc/uid/TP40007899-CH4-SW1">Default e-mail application</a></td>
<td rowspan="4" valign="top"><code>mailto:&lt;email&gt;?query</code></td>
<td><code>Subject</code></td>
</tr>
<tr>
<td><code>CC</code></td>
</tr>
<tr>
<td><code>BCC</code></td>
</tr>
<tr>
<td><code>Body</code></td>
</tr>
<tr>
<td valign="top"><a href="https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/PhoneLinks/PhoneLinks.html#//apple_ref/doc/uid/TP40007899-CH6-SW1">Default phone application</a></td>
<td><code>tel:&lt;number&gt;</code></td>
<td valign="top">N/A</td>
</tr>
<tr>
<td valign="top"><a href="https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/SMSLinks/SMSLinks.html#//apple_ref/doc/uid/TP40007899-CH7-SW1">Default SMS application</a></td>
<td><code>sms:&lt;number&gt;</code></td>
<td valign="top">N/A</td>
</tr>
<tr>
<td valign="top">Syndication feed reader</td>
<td><code>feed:&lt;url&gt;</code></td>
<td valign="top">N/A</td>
</tr>
<tr>
<td valign="top">Apple FaceTime</td>
<td><code>facetime:&lt;number&gt;</code></td>
<td valign="top">N/A</td>
</tr>
</tbody>
</table>
http://handleopenurl.com/  可以查看其它协议相关接口，比如qq

## 2、打开自定义app

首先在要打开的比如app2中 定义在info.plist URL Schemas


<pre class="wrap:true lang:objc decode:true" title="url open delegate">在app1中打开
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@“myapp://”]];

打开回调
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation   (Available in iOS 4.2 and later)

- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL*)url (ios 4.2 Deprecated)</pre>
&nbsp;