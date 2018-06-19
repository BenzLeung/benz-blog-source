---
title: 作品：AMR 录音机
date: 2018-02-14 00:17:48
tags:
---

AMR 是一种音频文件格式，专用于语音。它一般采用很低的采样率和码率，使得文件非常小。而缺点就是音频质量会比较差，因此不适用于音乐。但若只是用于语音，只要能听清楚对方在说什么即可。微信的语音功能所用的就是AMR格式。

`benz-amr-recorder` （AMR 录音机）是一个 JavaScript 库，**纯前端**编码、解码、播放、录音 AMR 音频，无须任何服务器支持。适用于网页版的语音聊天应用、录音机应用等。

 - [项目 Github 页](https://github.com/BenzLeung/benz-amr-recorder)
 - [Demo 页](https://benzleung.github.io/benz-amr-recorder/demo.html)

特性：

 - 方便的 API 实现解码、播放、录音、编码 AMR 文件。
 - 支持 url 和 blob （即`<input type="file">`）方式获取 AMR。
 - 支持将浏览器 `<audio>` 所支持的音频格式（例如 MP3 或 OGG 音频）转换成 AMR 音频。
 - 编码后的 AMR 文件可下载，无须服务器。

支持浏览器：

 - **桌面浏览器：** 除IE之外基本都支持。
 - **iOS：** 播放需要 iOS 6+，录音需要 iOS 11+。
 - **Android：** Android 5.0+，Chrome 53+ 或采用此核心的其他浏览器，或者 Firefox 42+。

若有问题，欢迎提交 [Issue](https://github.com/BenzLeung/benz-amr-recorder/issues)。
