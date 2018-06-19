---
title: JPG？GIF？PNG？前端如何选择图片格式？
date: 2017-12-09 16:33:08
tags:
---

最近准备给团队传授图片格式的知识，于是开了谷歌，欲找些 PPT 素材，却发现似乎没有人**好好写过**与图片相关的知识，要么只是照搬一些百科的内容，要么写得太多 CRC、Gamma 之类的**专(zhuang)业(B)术语**，要么花了大量篇幅介绍它们的全称和历史（知道了全称和历史也不能把图片用得更好啊）。于是我果断自己写一篇。

其实，对于 JPG、GIF、PNG 这3种格式的选择，其套路非常简单粗暴——

<!-- more -->

> 照片用 **JPG**。
>
> 动画用 **GIF**。
>
> Logo、Icon 等小图用 **PNG-8**。
>
> 非特殊情况，尽量不要用 PNG-24 和 PNG-32。

而 **WebP** 这种谷歌发明的新格式，虽然比上述任何一种格式都要好，但是截至现在（2017年10月），它的浏览器兼容性还不行，所以暂时还不能用（或者用两套图片，不过我嫌麻烦）。

![WebP 格式的兼容性（截图自 caniuse 网站）](https://upload-images.jianshu.io/upload_images/5831473-8898ffb67b096b56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

另外，最近还流行矢量图片 SVG 和 Icon Font，本文暂时不介绍这俩，以后有机会我再写。

好了，懒人看到这就可以关掉本文章了*（或者直接拉到最后，打赏点赞加评论）*。

----------------------

下面介绍一些比较原理性的知识（但也不是很原理，反正像 CRC 呀 Gamma 呀之类的词我是不会用的）。

## 主要内容

- 从早期的 BMP 格式讲起
- 颜色表简介和作用
- PNG 的颜色表和特性
- GIF 的特性
- JPG 的特性
- JPG 的“连续”格式
- JPG、GIF、PNG 功能对比
- 所以如何选择格式（结论上文已经给出，下文详解原因）
- Photoshop 保存图片的正确姿势
- tinypng.com - 让文件再小一点
- 附加内容1：元数据
- 附加内容2：色彩空间

基本上，我可以自信地说——关于前端攻城狮和设计狮如何使用 JPG、GIF、PNG，**看本文就够了**。

## 从早期的 BMP 格式讲起

BMP 格式图片，取自英文单词 **B**it**m**a**p**，Windows 中文版译作**“位图”**（不知道微软什么人的烂翻译），是早期 Windows 系统（XP以及更早期）所用的图片格式。

BMP 的文件结构很简单，没有压缩，一个一个像素地记录起来。

## BMP 的分类

Windows 自带的经典应用《画图》支持以下4种 BMP 格式：

![《画图》所支持的4种 BMP 格式](https://upload-images.jianshu.io/upload_images/5831473-8a7f306a26c6b2c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 单色位图（1位）——1字节记录8个像素，图片一般只有黑和白。
- 16色位图（4位）——1字节记录2个像素，图片有16种颜色。
- 256色位图（8位）——1字节记录1个像素，256种颜色。
- 24位位图——3个字节记录1个像素，红、绿、蓝三原色各占一个字节，总共大约1670万色，即**真彩色**。

> 给小白补一波计算机基础知识：计算机数据是用二进制数字来存储的，1个字节就是一个8位的二进制数字，1024字节就是1KB，1024KB就是1MB，1024MB就是1GB。
>
> 4位二进制数字，换成十进制可表示从0到15，共16个数，所以16色位图每个像素占用4位，即半个字节。
>
> 8位二进制数字，换成十进制就是从0到255，所以256色位图每个像素占一个字节。

那么问题来了……

## 16色、256色位图都有哪些颜色？

16色、256色图片文件除了存储每个像素之外，还会存储一份**颜色表**（**Palette**，亦译作调色板、色盘），从真彩色的1670万种色彩里任意挑选16、256种颜色，构成一个索引。颜色表大概长这个样子：

> 0号色：#000000
>
> 1号色：#3385FF
>
> 。。。
>
> 255号色：#FAFEF2

在 Photoshop 里打开一张256色图片，然后点击菜单 **图像 → 模式 → 颜色表**，可以看到当前图片的颜色表：

![Photoshop 里查看颜色表](https://upload-images.jianshu.io/upload_images/5831473-bcd6e71539ddf9b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 游戏机的颜色表

![游戏机的颜色表](https://upload-images.jianshu.io/upload_images/5831473-88e4c5f821dc5423.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把颜色表用得最666的要数80年代的FC游戏机了。

![《超级马里奥》使用颜色表实现金币闪亮效果](https://upload-images.jianshu.io/upload_images/5831473-564a50b933dfc886.gif?imageMogr2/auto-orient/strip)

是不是瞬间明白了颜色表是什么鬼？

## 颜色表的作用

对于 BMP 图片来说，若图片尺寸相等，16 色 BMP 的文件大小当然比 256 色 BMP 要少一半，因为可以用更少的字节数（位数）来存储一个像素。

而 GIF 和 PNG 图片更高端，除了支持16色、256色的颜色表之外，还可以支持256色以内的任意颜色数。例如222色、111色、6色7色8色……它们都能支持。

所以……

**颜色数越少，文件体积越小。**

**颜色数越少，文件体积越小。**

**颜色数越少，文件体积越小。**

## 带颜色表的 PNG 和不带颜色表的 PNG

PNG可以大致分为3种：**PNG-8**、**PNG-24**、**PNG-32**。

其中后面那个数字8、24、32代表位数，就像上述的BMP分4位（16色）、8位（256色）、24位（真彩色）一样。

PNG 除了24位外还能有32位，除了红、绿、蓝三原色之外，还有个透明通道，从0-255表示从透明到半透明到不透明。

PNG-8 带颜色表，而且不像 BMP 只有固定的16色、256色，PNG-8 的颜色数可以从2种到256种之间任意种，像123种、10种颜色之类的颜色数，PNG-8 都是支持的。而且通过强大的压缩算法，PNG-8 可以真正做到颜色数越少，文件体积越小。

## 带颜色表的 GIF

GIF 图片是带颜色表的，它最多支持256色，也支持256以内任意数量的颜色表，但是**不支持真彩色**。

GIF 的特性是**帧动画**。

## 不带颜色表的 JPG

![截图来源于PS CC2017：文件 → 导出 → 存储为 Web 所用格式（旧版）](https://upload-images.jianshu.io/upload_images/5831473-b710d4f85b7ae9fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

JPG 不支持颜色表，也不支持透明，只有真彩色。

JPG 的特性是**有损压缩**。对于网络传输来说，有损压缩是个优点。它可以削减一些肉眼难以察觉的细节，让图片体积大大减小。有损压缩特别适用于相机拍摄的照片。

## “连续”格式的 JPG

![截图来源于PS CC2017：文件 → 存储为... → “JPG”格式 → 保存](https://upload-images.jianshu.io/upload_images/5831473-5aefe235b7053e7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

常用 Photoshop 的童鞋可能已经发现，另存为JPG的选项对话框（如上图）中，有个“连续”选项。这是什么意思呢？

![“基线”格式的JPG加载过程：从上往下](https://upload-images.jianshu.io/upload_images/5831473-3031e966db4e6b7d.gif?imageMogr2/auto-orient/strip)

![“连续”格式的JPG加载过程：从模糊到清晰](https://upload-images.jianshu.io/upload_images/5831473-5e196fd853f0dd7e.gif?imageMogr2/auto-orient/strip)

“基线”和“连续”格式的JPG，主要是在网速比较慢的时候，图片加载方式不同。如上面两个动图所演示。

## JPG、GIF、PNG 功能对比

|                       | JPG | GIF | PNG-8 | PNG-24 | PNG-32 |
| -----------------:|:-------:|:-----:|:--------:|:-----------:|:-----------:|
| 颜色表支持    |          |  ◉  |    ◉    |               |                |
| 真彩色支持    |   ◉   |        |           |     ◉       |      ◉      |
| 有损压缩       |   ◉   |        |            |               |                |
| 帧动画           |          |  ◉  |           |                |                |
| 透明像素支持 |         |  ◉  |    ◉    |               |      ◉       | 
| 半透明度支持 |         |        |    ◉   |                |      ◉       |

JPG的主要特性是**有损压缩**。

GIF的主要特性是**帧动画**。

PNG的主要特性是**半透明**。

（注意GIF不支持半透明，只能完全透明或者不透明。）

## 如何选择格式？

> 照片用 **JPG**。
>
> 动画用 **GIF**。
>
> Logo、Icon 等小图用 **PNG-8**。
>
> 非特殊情况，尽量不要用 PNG-24 和 PNG-32。

这是本文开头的直接总结。

下面讲原因。

![同一张图，PNG-8 的体积小于 GIF](https://upload-images.jianshu.io/upload_images/5831473-cee8afd7d6a43074.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先，Logo、图标等小图片，整张图片一般不多于256色，因此当然选择带颜色表的图片格式。而事实证明，对于同一张图，PNG-8 的体积是小于GIF的。因此，**若不是动画，小图片请用 PNG-8**。

然后，照片、横幅等大图，就像在线听歌一样。如果在线听无损音乐，那得很大的带宽才行。我们在线试听歌曲，目的就是听听这首歌，不会非常注重音质（而且我的木耳是听不出MP3和无损的区别的）。同样道理，放在网页中的大图，用户肯定不会太看重画质，能看清楚内容就行。因此，**照片大图用有损压缩的JPG**。

根据经验，**JPG的画质一般选择 60% - 70%**。当然如果你要求较高，可以在PS里一边看预览一边慢慢调整直到自己满意。

## Photoshop 保存图片的正确姿势

![① 文件 → 导出 → 导出为...](https://upload-images.jianshu.io/upload_images/5831473-daf953e4b9c9a495.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![②选格式 ③元数据“无” ④不嵌入颜色配置](https://upload-images.jianshu.io/upload_images/5831473-20c915d9fc52822b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![PNG：勾选“较小文件（8位）”即可保存为 PNG-8](https://upload-images.jianshu.io/upload_images/5831473-c953c48bc3efdfa9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![GIF 无特殊选项](https://upload-images.jianshu.io/upload_images/5831473-12afedd2d0007d7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![JPG：若你不想慢慢调整，品质直接选择60%](https://upload-images.jianshu.io/upload_images/5831473-8c5942014ae3b386.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 文件 → 导出 → 导出为...
2. 选择格式
    - PNG：勾选**“较小文件（8位）”**即可保存为 PNG-8
    - JPG：若你不想慢慢调整，品质直接粗暴地选择**60%**一般不会错
3. 元数据选“无”
4. 勾选“转换为sRGB”，不要勾选“嵌入颜色配置”

## tinypng.com - 让文件再小一点

![tinypng.com - 一个帮助压缩图片的网站](https://upload-images.jianshu.io/upload_images/5831473-45104468a89d2e11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[tinypng.com](https://tinypng.com/) 是一个帮助进一步压缩PNG和JPG图片的网站，使用方法很简单，只要打开网站，然后把需要压缩的图片从桌面拖入这个网站中间的框里，然后坐等几秒钟，就可以下载被压缩的图片了。

据网站的介绍，tinypng 可以有更好的压缩算法，而且通过智能地减少颜色数，达到有效的压缩。

而喜欢命令行的程序猿，也可以试试 [pngcrush](https://pmt.sourceforge.io/pngcrush/) 工具。

另外，腾讯ISUX团队也出品了一个类似 tinypng 的在线图片压缩工具，称为**[智图](http://zhitu.isux.us/)**。它除了 PNG 和 JPG ，还支持 WebP。

## 总结一下

- 16色（4位）、256色（8位）图片的颜色由**颜色表**决定。
- **减少颜色数**有助于减少图片体积。
- 照片用 **JPG**，动画用 **GIF**，图标用 **PNG-8**，尽量不用 PNG-24 和 PNG-32。
- PS保存图片的正确姿势：文件 → 导出 → 导出为...
- 第三方工具：[tinypng.com](https://tinypng.com/)、[pngcrush](https://pmt.sourceforge.io/pngcrush/)、[腾讯智图](http://zhitu.isux.us/)

以上就是本文的全部核心内容，但是我还不打算在此结束本文。

------------------------------------

在上文“Photoshop 保存图片的正确姿势”中， “导出为”对话框内提及了两个姿势：**元数据**选“无”、**色彩空间**中勾选“转换为sRGB”，不要勾选“嵌入颜色配置”。那么，元数据和色彩空间到底是什么鬼？为什么要用上述姿势？下面给两篇附加内容，让本文真正地做到“看这篇就够了”（自信脸）。

## 附加内容1：元数据

元数据是什么鬼？

![Photoshop：文件 → 文件简介...](https://upload-images.jianshu.io/upload_images/5831473-fcd80820dcae027e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 Photoshop 中打开一张照片，然后点击菜单 **文件 → 文件简介...** ，即可看到这张照片的元数据。

[维基百科](https://zh.wikipedia.org/wiki/%E5%85%83%E6%95%B0%E6%8D%AE)对元数据的解释是**描述数据的数据**。所以图片的元数据就是描述图片的数据咯。

带元数据的图片文件会额外地加个几KB，存储这张图片的**作者、拍摄日期、标题、版权、相机**等文本信息。它会占用一些空间（例如JPG的元数据最大占用64KB），但是网页里的图标或背景图的元数据是没有任何用处的（除非你要声明图片版权）。

因此，元数据选择**“无”**，把鸡肋的元数据清除，可以减小图片体积。

## 附加内容2：色彩空间

![sRGB 色彩空间](https://upload-images.jianshu.io/upload_images/5831473-ad35dceeae6f47c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注明一下版权：上图转自[维基百科](https://zh.wikipedia.org/wiki/SRGB%E8%89%B2%E5%BD%A9%E7%A9%BA%E9%97%B4)。

上图中，那大块彩色的像舌头的东东，表示人类眼睛所能看到的所有颜色。中间的三角形框框，表示一般显示器能显示出来的所有颜色，这个三角形框框内的颜色，就是 **sRGB 标准**。

而世界上并不仅仅有 sRGB 标准，毕竟 sRGB 只框住了全世界颜色不到一半。

![各种色彩标准](https://upload-images.jianshu.io/upload_images/5831473-1aaac65c618ad352.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注明一下版权：上图也转自[维基百科](https://en.wikipedia.org/wiki/Color_space)。

Photoshop 支持多种色彩空间，图片文件只要**嵌入颜色配置文件**，即可使用特定的色彩空间。

![Photoshop 新建文档，可以选择多种色彩空间](https://upload-images.jianshu.io/upload_images/5831473-adbca7ed912e7618.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果色彩空间不对，会让图片的色调要么变得过于暗淡，要么变得过于鲜艳。例如，Adobe RGB 标准的 #00FF00 （纯绿色）会比 sRGB 标准的同种颜色（#00FF00）看起来要更鲜艳。

Photoshop 导出图片时，**色彩空间**中勾选“转换为sRGB”，不要勾选“嵌入颜色配置”，就是为了解决图片色调不对的问题。

因为多数用户的屏幕只能显示 sRGB 标准的色彩，所以如果原图用了更高级的色彩空间，转换成sRGB就不需要额外嵌入颜色配置。毕竟，颜色配置也是占空间的。而且有些浏览器还会直接无视颜色配置（不支持色彩空间），直接按sRGB显示。

如果原图本来是 sRGB 的，那么勾选“转换为sRGB”将不会有任何作用。不过，若不知道原图的色彩空间，勾选它总是不会错的。

值得一提的是，从 iPhone 7 开始，苹果系列手机的屏幕和相机支持称为 **[DCI-P3](https://en.wikipedia.org/wiki/DCI-P3)** 的色彩空间，提供了比一般屏幕和相机（也就是 sRGB）更多的色彩。因此，把 iPhone 7 以上机型拍摄的图片传入电脑看，其效果很可能大大不如手机屏幕上看。

（完）
