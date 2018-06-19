---
title: 游戏：欢乐台球
date: 2017-03-08 16:13:06
tags:
---

为了学习 chipmunk 物理引擎，我自行策划并开发了一个台球游戏。随便取了一个名字《欢乐台球》。

- 项目Github：[https://github...billards](https://github.com/BenzLeung/funny-billards)
- 立即开始游戏：[https://benzleung...billards/](https://benzleung.github.io/funny-billards/)

用手机玩更带感（扫码开始玩）：

![QR Code](https://raw.githubusercontent.com/BenzLeung/funny-billards/master/res/qrcode.png "QR Code")

在开发这个游戏的同时，我实践了一波 Web Audio API 。因为我发现 cocos2d-js 自带的音频引擎无法提前加载音频文件，只有真正播放的时候才会加载，导致第一次播放音效时会延迟几秒。所以，我自己弄了一个简单的音频引擎，暂时取名为 Benz Audio Engine。

Benz Audio Engine 目前是作为这个游戏源码的一部分而存在，近期我会单独为它建立一个 Repository。

（**2017/03/13 更新：**Benz Audio Engine 已经建好 [Repository](https://github.com/BenzLeung/benz-audio-engine) 了！）

游戏的音效和背景音乐都截取自红白机游戏《花式撞球》（Side Pocket），它是一款于1987年问世的经典游戏，由日本 Data East 株式会社开发，日版由日本南梦宫株式会社（Namco）发行，美版由 Nintendo of America Inc. 发行。

图片资源都是我自己画的，原始 psd 文件也全部在 res 目录里面。而我并不是做设计的，所以界面可能会不太好看。

不知道为何，我就是对小时候的经典游戏特别的着迷。尤其是红白机（NES）的游戏音乐，听着让人兴奋。

做完了这个游戏，我对 cocos2d-js 应该算是上手了。接下来要研究它的高级功能。