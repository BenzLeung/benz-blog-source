---
title: 'Web Audio API 小教程(3): 初级使用篇'
date: 2017-03-15 20:25:40
tags: [Web Audio API,Web Audio API 小教程,Javascript]
---

（续上节：[Web Audio API 小教程(2): 入门篇](/2017/03/15/Web-Audio-API-Guide-Getting-Started/)）

- [(0): 序言与目录](/2017/03/15/Web-Audio-API-Guide-Preface/)
- [(1): 它能做什么？](/2017/03/15/Web-Audio-API-Guide-What-can-it-do/)
- [(2): 入门篇](/2017/03/15/Web-Audio-API-Guide-Getting-Started/)
- **(3): 初级使用篇**
- [(4): 一些声音常识](/2017/03/15/Web-Audio-API-Guide-About-Sound/)
- [(5): 进阶篇](/2017/03/15/Web-Audio-API-Guide-Advanced-User/)

[【Gitbook 版本】](https://benzleung.gitbooks.io/web-audio-api-mini-guide/)

## AudioNode 回顾

### 概念

<!-- more -->

- `AudioNode` 是一种用于生成或处理音频数据的类。
- `AudioNode` 是一个基础类，它有很多子类。
- 它的子类们，分别有不同的处理音频的功能
	- 例如均衡器、音量增益、滤波器……

### 种类

作为【声音源头】的 AudioNode。

- 用于存储波形数据，或者凭空生成波形数据。
- 音源没有输入，只有输出。
- 例： `SourceNode` ，用于存储波形数据并直接原样输出。

作为【过滤器】的 AudioNode。

- 用于过滤所输入的波形，并输出过滤后的结果。
- 过滤器至少接受1个输入，有1个输出。
- 有些过滤器可接受多个输入。
- 例：`GainNode`，用于调整音量。

作为【终点】的 `destination`。

- 这是Node的终点。
- 可接受多个输入（但不建议），无须输出。
- 输入到这里的声音将被用户听到。

### 代码

使用 `.connect` 方法把各种Node连接起来，起点是 `SourceNode`，终点是 `destination`：

```javascript
sourceNode.connect(node1);
node1.connect(node2);
node2.connect(node3);
// ......
nodeN.connect(ctx.destination);
```

使用 context 创建 AudioNode 对象，而不是 new 一个。

例如使用 `context.createBufferSource();` 而不是 new SourceNode();

```javascript
var sourceNode = ctx.createBufferSource();
```
----------

## 常用的 Node 详解

下面会详解3个最常用的 Node。

### SourceNode

这是最常用的 Node ，用于存储波形数据。

创建方法：

```javascript
var sourceNode = ctx.createBufferSource();
```

提供数据：

```javascript
sourceNode.buffer = buffer;
```

例：

```javascript
function playBuffer(buffer) {
  var sourceNode = ctx.createBufferSource();
  sourceNode.buffer = buffer;
  sourceNode.connect(ctx.destination);
  sourceNode.start(0);
}
```

#### SourceNode.start() 开始播放

```javascript
start(when, offset, duration)
```

- `when`: 何时开始播放（秒）（参数可省掉）
- `offset`: 音频的何处开始播放（秒）（参数可省掉）
- `duration`: 播放多长时间（参数可省掉）
- 返回值：无

例：

```javascript
sourceNode.start(ctx.currentTime + 5, 1.5, 3.0);
```

上述语句表示：5秒后开始播放（当前时间+5），从音频1.5秒开始，播放到4.5秒处停止（持续3.0秒）。

若 `when` 参数的值小于等于 `ctx.currentTime` ，则默认立即开始播放。下面 `stop()` 也一样。


#### SourceNode.stop() 结束

	stop(when)

- `when`: 何时结束（秒）（参数可省掉）
- 返回值：无

例：

```javascript
sourceNode.stop(ctx.currentTime + 5);
```

上述语句表示：5秒后结束（当前时间+5），然后**整个 SourceNode 就作废了**（上一节讲过），要播放就需要重新创建对象。

#### SourceNode.loop / loopStart / loopEnd 循环播放设定

```
loop = [boolean]
loopStart = [number]
loopEnd = [number]
```

- `loop`: 是否循环播放
- `loopStart`: 循环开始点
- `loopEnd`: 循环结束点

例：

```javascript
sourceNode.loop = true;
sourceNode.loopStart = 2.2;
sourceNode.loopEnd = 6.0;
```

上述语句表示：2.2秒-6.0秒是循环节，播放到6.0秒处立即返回2.2秒继续播放。

可以指定循环节位置，是 SourceNode 比 HTML-5 的 `<audio>` 更强大的地方之一。所以，在游戏开发中，即使背景音乐也应该用 Web Audio API 而不用 `<audio>`。

#### SourceNode.onended 播放完毕事件

```
onended = [function]
```

- `onended`: 播放完毕后触发的事件

例：

```javascript
sourceNode.onended = function () {
  alert('播放完毕！');
};
```

### GainNode 音量调节器

- 用于调节音量
- 创建方法： `ctx.createGain()`
- 调音量： `gainNode.gain.value = 0.5`（取值范围 0.0-1.0）

例：（把上文以及上一节的 playBuffer() 加上音量功能）

```javascript
function playBuffer(buffer, volume) {
  var sourceNode = ctx.createBufferSource();
  sourceNode.buffer = buffer;
  var gainNode = ctx.createGain();
  gainNode.gain.value = volume;
  sourceNode.connect(gainNode);
  gainNode.connect(ctx.destination);
  sourceNode.start(0);
}
```

上述代码，Node 连接成 `sourceNode` -> `gainNode` -> `destination`。然后，可以随时通过修改 `gainNode.gain.value` 的值，来调节音量。

#### GainNode.gain

```
gain = [AudioParam]
```

- 使用 `gainNode.gain.value` 调音量。
- `gain` 是一个 `AudioParam` 对象，`gain.value` 才是音量的具体值（0.0-1.0）。
- **AudioParam** 是个 Audio API 特有的对象（下文分解↓↓）

### AudioParam

**AudioParam** 不是 Node，而是一个很有用的类。

某些 Node 的**数值型参数**可采用 AudioParam 记录数值。

AudioParam 不仅可以直接给 `value` 一个固定值，而且能让数值产生**渐变**效果，例如让音量从高慢慢变低，产生淡出效果。

#### AudioParam.value

```
value = [number]
```

- 直接改数值。

例：

```javascript
gainNode.gain.value = 0.5;
```

#### AudioParam.setValueAtTime()

```
setValueAtTime(value, startTime)
```

- 在指定时间把数值改成指定值。
- value: 指定值。
- startTime: 指定时间。

例：

```javascript
gainNode.gain.setValueAtTime(0.5, ctx.currentTime + 0);
gainNode.gain.setValueAtTime(1.0, ctx.currentTime + 1);
```

上述代码将在0秒处设置关键帧，音量为0.5。然后在1秒处设置关键帧，音量为1.0。

音量与时间的关系如下图所示：

![setValueAtTime 线性图](/web-audio-api-demo/guide-img/2/1.png "setValueAtTime 线性图")

#### AudioParam.linearRampToValueAtTime() 线性渐变

```
linearRampToValueAtTime(value, endTime)
```

- 把数值**线性渐变**到指定值
- value: 指定值
- endTime: 指定时间

例：

```javascript
gainNode.gain.setValueAtTime(0.5, ctx.currentTime + 0);
gainNode.gain.linearRampToValueAtTime(1.0, ctx.currentTime + 1);
```

上述代码将在0秒处设置关键帧，音量为0.5。然后在1秒处设置**线性渐变关键帧**，音量为1.0。于是从上一关键帧到该关键帧，音量将产生线性渐变效果。

音量与时间的关系如下图所示：

![linearRampToValueAtTime 线性图](/web-audio-api-demo/guide-img/2/2.png "linearRampToValueAtTime 线性图")

#### AudioParam.exponentialRampToValueAtTime() 指数渐变

```
exponentialRampToValueAtTime(value, endTime)
```

- 把数值**指数渐变**到指定值
- value: 指定值
- endTime: 指定时间

例：

```javascript
gainNode.gain.setValueAtTime(0.5, ctx.currentTime + 0);
gainNode.gain.exponentialRampToValueAtTime(1.0, ctx.currentTime + 1);
```

上述代码将在0秒处设置关键帧，音量为0.5。然后在1秒处设置**指数渐变关键帧**，音量为1.0。于是从上一关键帧到该关键帧，音量将产生指数渐变效果。

音量与时间的关系图，（我画不出来 555~ 谁想办法帮画个？）

#### AudioParam.setValueCurveAtTime() 平滑曲线

```
setValueCurveAtTime(values, startTime, duration)
```

- 在一段时间之内按照数组内的值一个个地平滑变化
- values: (数组)指定值
- startTime: 开始时间
- duration: 持续时长

例：

```
gainNode.gain.setValueCurveAtTime([0.0,0.2,0.5,0.1], ctx.currentTime + 0, 10);
```

上述代码将在10秒内，音量以 0.0→0.2→0.5→0.1 平滑过渡。

音量与时间的关系图，（我画不出来 555~ 谁想办法帮画个？）

#### AudioParam 一个例子

![AudioParam 例子](/web-audio-api-demo/guide-img/2/audioparam-automation1.png "AudioParam 例子")

```javascript
var t0 = 0;
var t1 = 0.1;
var t2 = 0.2;
var t3 = 0.3;
var t4 = 0.4;
var t5 = 0.6;
var t6 = 0.7;
var t7 = 1.0;

var curveLength = 44100;
var curve = new Float32Array(curveLength);
for (var i = 0; i < curveLength; ++i) {
    curve[i] = Math.sin(Math.PI * i / curveLength);
}

param.setValueAtTime(0.2, t0);
param.setValueAtTime(0.3, t1);
param.setValueAtTime(0.4, t2);
param.linearRampToValueAtTime(1, t3);
param.linearRampToValueAtTime(0.15, t4);
param.exponentialRampToValueAtTime(0.75, t5);
param.exponentialRampToValueAtTime(0.05, t6);
param.setValueCurveAtTime(curve, t6, t7 - t6);
```

（本例来源：<a href="https://webaudio.github.io/web-audio-api/#audioparam-automation-example" target="_blank">https://webaudio.github.io/web-audio-api/#audioparam-automation-example</a>）

### DynamicsCompressorNode 动态压缩器

这是一个动态混音器，用于把多个音源实时混音，并防止爆音。

**创建方法：**`ctx.createDynamicsCompressor()`

**用法：**直接把多个音源 connect 到这个Node即可。

例：

```javascript
var compressorNode = ctx.createDynamicsCompressor();
sourceNode1.connect(compressorNode);
sourceNode2.connect(compressorNode);
sourceNode3.connect(compressorNode);
compressorNode.connect(ctx.destination);
```

虽然可以把多个 Node 直接 connect 到 ctx.destination，但是不建议这么做，因为这样可能会出现爆音现象。若有同时播放多个声音的需求（例如一个游戏的各种音效），记得**通过 DynamicsCompressorNode 混合多个声音**。

## 接下来

下一节：[Web Audio API 小教程(4): 一些声音常识](/2017/03/15/Web-Audio-API-Guide-About-Sound/)

在继续介绍 Web Audio API 的高级玩法之前，我觉得有必要先普及一些声音常识。因为有些高级玩法要求一些乐理知识。

所以，下一节没有代码。

## 一个更好的选择

如果觉得 Web Audio API 写起来代码量太大，那么可以考虑使用我已经封装好的 Javascript 库 —— **Benz Audio Engine**。

项目 Github：[https://github.com/BenzLeung/benz-audio-engine](https://github.com/BenzLeung/benz-audio-engine)

这个库**仅仅**使用了本节所提到的3个 Node 。简单、干净，没有多余的功能。

## 参考文献

- [Web Audio API W3C Working Draft](https://webaudio.github.io/web-audio-api/)
