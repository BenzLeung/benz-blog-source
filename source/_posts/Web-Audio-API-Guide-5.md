---
title: 'Web Audio API 小教程(5): 进阶篇（更多API）'
date: 2017-03-15 20:30:14
tags: [Web Audio API,Web Audio API 小教程,Javascript]
---

（续上节：[Web Audio API 小教程(4): 一些声音常识](/2017/03/15/Web-Audio-API-Guide-About-Sound/)）

- [(0): 序言与目录](/2017/03/15/Web-Audio-API-Guide-Preface/)
- [(1): 它能做什么？](/2017/03/15/Web-Audio-API-Guide-What-can-it-do/)
- [(2): 入门篇](/2017/03/15/Web-Audio-API-Guide-Getting-Started/)
- [(3): 初级使用篇](/2017/03/15/Web-Audio-API-Guide-Primary-User/)
- [(4): 一些声音常识](/2017/03/15/Web-Audio-API-Guide-About-Sound/)
- **(5): 进阶篇**

[【Gitbook 版本】](https://benzleung.gitbooks.io/web-audio-api-mini-guide/)

这一节主要列出一些 API ，这些 API 或者是我觉得比较好玩的，或者是我觉得在做音乐播放器或者游戏时有很大可能用得着的。

Web Audio API 中每个 API 可能有很多专业的高级属性和方法，有些（我认为）比较难用到的这里就省略了。

这里列出的 API 都有我自制的 Demo，纯手工制作，如有雷同，纯属巧合。

<!-- more -->

**[【全部 Demo 的目录】](/web-audio-api-demo/)**

----------

### DynamicsCompressor - 动态混音

**[【Demo】](/web-audio-api-demo/mix.html)**

用于把多个音源实时混音，并防止爆音。

**对象创建方法：**`ctx.createDynamicsCompressor()`

**用法：**直接把多个音源 connect 到这个Node即可。

虽然可以把多个 Node 直接 connect 到 ctx.destination，但是不建议这么做，因为这样可能会出现爆音现象。若有同时播放多个声音的需求（例如一个游戏的各种音效），记得**通过 DynamicsCompressorNode 混合多个声音**。

----------

### Panner - 3D立体环绕音效

**[【Demo】](/web-audio-api-demo/panner.html)**

把声音的声源移动到3D空间中指定的点 (x, y, z)，模拟3D空间音效。

可以在声音播放期间实时调整空间坐标值 (x, y, z)，让声音绕着你转圈圈。

**对象创建方法：**`ctx.createPanner()`

**常用属性方法：**

- **PannerNode.setPosition(x, y, z)**
	- 设置声源空间位置 (x, y, z)。(x, y, z)是代表位置的坐标值。
- **PannerNode.setOrientation(x, y, z)**
	- 设置声源的朝向 (x, y, z)。(x, y, z)是代表方向的向量值。一个人背对你说话和正对你说话，效果是不一样的。
- **PannerNode.setVelocity(x, y, z)**
	- 设置声源的运动方向和速度 (x, y, z)。(x, y, z)是代表方向和速度的向量值。一个人站着说话和一边跑步一边说话，效果也是不一样的。

----------

### BiquadFilter - 滤波器、均衡器

**[【Demo】](/web-audio-api-demo/eq.html)**

过滤或增益指定频率的声波。

**对象创建方法：**`ctx.createBiquadFilter()`

**常用属性方法：**

- **BiquadFilterNode.type**
	- 指定滤波器类型（详见下表）
- **BiquadFilterNode.frequency**
	- 指定一个频率（详见下表）
- **BiquadFilterNode.Q**
	- 设定容许度（详见下表）
- **BiquadFilterNode.gain**
	- 设定增益值（详见下表）

| type | 用途 | frequency | Q | gain |
| ---- | --- | --------- | - | ---- |
| lowpass | 低通滤波，只允许低频声音通过。 | 能通过的频率值界线（Hz），例如440，即只让 0-440Hz 的声音通过，削减掉 440-99999Hz 的声音。 | 削减度，数值越小则声音削减越厉害。 | 不可用。 |
| highpass | 高通滤波，只允许高频声音通过。 | 能通过的频率值界线（Hz），例如440，即只让 440-99999Hz 的声音通过，削减掉 0-440Hz 的声音。 | 削减度，数值越小则声音削减越厉害。 | 不可用。 |
| peaking | 峰值滤波，增益或削减某一频率的声音 | 要增益或削减的频率值**中间点**（Hz）。 | 范围，例如频率 440Hz，范围Q是20，那么范围就是 430-450Hz。 | 增益或削减度，正值增益，负值削减。 |
| bandpass, lowshelf, highshelf, notch, allpass | 带通滤波, 低架滤波, 高架滤波, 陷波器, 全通滤波 | 不常用，略 | 不常用，略 | 不常用，略 |


----------

### Analyser - 频谱分析器（频谱图、时域图）

**[【Demo 1，频谱图】](/web-audio-api-demo/analyser.html)**

**[【Demo 2，时域图】](/web-audio-api-demo/analyser2.html)**

获得音频的频谱数据，可用于绘制频谱图（条状的）和时域图（线状的）。

它不会改变声音波形，只读取波形数据。也就是说，输入和输出的声波是完全一样的，输入什么就输出什么。

**对象创建方法：**`ctx.createAnalyser()`

**常用属性方法：**

- **Analyser.fftSize**
	- 频谱分析下的快速傅里叶变换的大小（难理解？没事，看下一个属性就好），取值范围是 **32-2048 之内 2 的整数次方**，即 32、64、128、256、512、1024、2048。默认值为 2048。
- **Analyser.frequencyBinCount**
	- fftSize 值的一半。这个值代表你要绘制的条形频谱图中有多少条条形。**注意，这是个只读的属性**，要修改这个属性的值请改 fftSize。
- **Analyser.smoothingTimeConstant**
	- 让频谱图的动画带有平滑过渡效果，取值范围 0.0-1.0，0.0 代表不平滑，1.0 代表很平滑。
- **Analyser.getByteFrequencyData(array)**
	- 把当前的即时**频谱图**数据复制到 array 数组里。array 数组须事先创建好，Uint8Array 类型，数组大小为 frequencyBinCount，即应该事先有以下这一代码：
	- `var array = new Uint8Array(analyser.frequencyBinCount);`
- **Analyser.getByteTimeDomainData(array)**
	- 把当前的即时**时域图**数据复制到 array 数组里。array 数组须事先创建好，Uint8Array 类型，数组大小为 frequencyBinCount，即应该事先有以下这一代码：
	- `var array = new Uint8Array(analyser.frequencyBinCount);`

----------

### Convolver - 卷积运算处理（环境混响音效）

**[【Demo】](/web-audio-api-demo/convolution.html)**

对音频进行卷积运算，用于添加环境音效，例如房间、音乐厅、剧院、电话等等。

在声学中，**回声**可以表示为声波与“特定波形”的**卷积**。

这些“特定波形”可以反映房间回声、教堂回声、电话声等等，并且可以用WAV格式存储。

参考：[维基百科：卷积](https://zh.wikipedia.org/wiki/%E5%8D%B7%E7%A7%AF)

（其实卷积到底是什么鬼，我也不太明白，维基百科的解释我看得似懂非懂、不明觉厉。）

（总之不管卷积了，能实现环境混响音效就好 ^_^）

**对象创建方法：**`ctx.createConvolver()`

**常用属性方法：**

- **ConvolverNode.buffer**
	- 把需要做卷积运算的波形数据扔进这个属性，就像 SourceNode.buffer 一样个扔法。然后输入的声波将会与这个 buffer 数据进行卷积运算后输出。

**注1：** 用于卷积运算的特定波形必须使用**无损**格式存储。也就是说可以是WAV格式的，但不能是MP3、AAC等有损压缩格式。

**注2：** 可以在这儿找到想要的卷积波形WAV文件——

- [包括大量卷积文件的 Github](https://github.com/GoogleChrome/web-audio-samples/tree/gh-pages/samples/audio/impulse-responses)
- [这大量卷积文件的 Demo](https://googlechrome.github.io/web-audio-samples/samples/audio/convolution-effects.html)

----------

### Oscillator - 振荡器（声波生成器）

**[【Demo 1，简易电子琴】](/web-audio-api-demo/oscillator.html)**

**[【Demo 2，铃儿响叮当】](/web-audio-api-demo/jinglebell.html)**

**[【Demo 3，各种波形对比】](/web-audio-api-demo/oscillator2.html)**

发出指定频率和指定形状的声波。

这是一种作为音源的 Node，没有输入（即其他Node不能connect它）。

**对象创建方法：**`ctx.createOscillator()`

**常用属性方法：**

- **OscillatorNode.type**
	- 指定声波类型，有五种值：方形波`square`、三角波`triangle`、正弦波`sine`、锯齿波`sawtooth`、自定义`custom`。
- **OscillatorNode.frequency**
	- 指定一个频率

**注1：** 使用振荡器可以实现**零流量**演奏音乐，但是需要一定的乐理知识。

**注2：** 一般来说，方形波和锯齿波用于演奏高音和中音，三角波和正弦波用于演奏低音。

**注3：** 可以在维基百科找到音高和频率的对照表——

- [维基百科：音高频率表](https://zh.wikipedia.org/wiki/%E9%9F%B3%E9%AB%98#.E9.9F.B3.E9.AB.98.E9.A0.BB.E7.8E.87.E8.A1.A8)

----------

## 完结了。

这个小教程就先写到这里为止了，然而 Web Audio API 的内容并不止于此。

还有一些更加高级的 API，我觉得咱们平民大众应该这辈子都用不着的了。那些这里没有提及的 API 估计是用来把 Adobe Audition 移植成网页版的吧。

（注：Adobe Audition 一个著名的音频处理软件，很强大的。）

## 本节的内容都用不着？

那么可以考虑使用我已经封装好的 Javascript 库 —— **Benz Audio Engine**。

项目 Github：[https://github.com/BenzLeung/benz-audio-engine](https://github.com/BenzLeung/benz-audio-engine)

这个库仅仅用到了本节所提到的第一个 API —— DynamicsCompressor，以及 SourceNode 和 GainNode（可能后续会考虑加入 Panner）。简单、干净，没有多余的功能。

好了，如果写得不好，欢迎提建议。