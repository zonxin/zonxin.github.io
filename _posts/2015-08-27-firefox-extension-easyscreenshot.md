---
layout: post
title: "2015在线笔试神器-1"
categories: 编程语言
tags: 浏览器开发 firefox
---

> 其实就是一个 firefox 截图扩展而已了。

截图插件/扩展各大浏览器都有不少，但是他们不了解"中国国情"，基本操作是先选择一个截图的区域，然后切换到另一个地方编辑，选择保存的位置。截图扩展并不难做，所以大家都认为做好编辑才是最重要的。但是有时候我们并不希望编辑截图和切换到其他界面。

本扩展的特色功能就是静默保存，不切换页面，不提示成功，不提示失败，不会令当前页面失去焦点。网页不能以任何形式检测截图软件的存在（网页可能会监测键盘事件，这时可以选择用鼠标操作或是更换快捷键,或是结合 EventBlockGen（还没写 coming soon ））。

[Fork EasyscreenshotSS on github][forkFromGithub]
感谢 [EasyScreenShot][MozillaOnline]

1. 安装方法，先安装 [firefox][firefoxDownload]
2. (其实是我偷懒了，对不起原扩展)在 firefox 中,按下`Ctrl+Shift+A`,打开附件组建管理器，选择扩展，如果安装的是 firefox 中国版会默认安装如下扩展,先卸载（其他截图插件可以不卸载）

   ![firefox 默认插件][firefoxext-origin]
3. [下载扩展]({{ "/download/easyscreenshot@zong.com.xpi" | prepend:site.baseurl }} "下载链接")（或者直接用firefox 打开），然后用 firefox 打开，选择安装。
4. 重启之后，在 firefox 界面的某个位置会出现 ![图标][icon] ,点击右侧三角形在下拉菜单中选择"设置"，做如下设置

    ![设置][setting]
5. 然后就可以通过快捷键（如果设置了），或是如上一步所示的方法在菜单中选择 "截取xxx" 选择截图区域。

#### 注意事项：

1. 第一次使用"截取选定区域" 的时候会有一个提示，如果关闭这个提示会导致 window Onblur事件

证明（如果看的懂的话）:

# ![一次愉快的截图][result]
 
感谢 [Mozilla][Mozilla] 基金会，感谢 [MozillaOnline][MozillaOnline].

[forkFromGithub]:https://github.com/zonxin/easyscreenshotSS
[firefoxDownload]:http://www.firefox.com.cn/download/ '火狐下载'
[firefoxext-origin]:{{ "/image/firefoxext/easyscreenshotSS-origin.jpg" | prepend:site.baseurl }}
[Mozilla]:https://www.mozilla.org/en-US/ 'Mozilla 基金会'
[MozillaOnline]:https://addons.mozilla.org/zh-CN/firefox/user/mozillaonline/?src=api#my-submissions 'MozillaOnline'
[icon]:{{ "/image/firefoxext/icon.jpg" | prepend:site.baseurl }} '图标'
[setting]:{{ "/image/firefoxext/setting.jpg" | prepend:site.baseurl }}
[result]:{{ "/image/firefoxext/result.jpg" | prepend:site.baseurl }}
