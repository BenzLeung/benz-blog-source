---
title: 'Web Audio API 小教程(2): 入门篇'
date: 2017-03-15 20:18:00
tags: [Web Audio API,Web Audio API 小教程,Javascript]
---

（续上节：[Web Audio API 小教程(1): 它能做什么？](/2017/03/15/Web-Audio-API-Guide-What-can-it-do/)）

- [(0): 序言与目录](/2017/03/15/Web-Audio-API-Guide-Preface/)
- [(1): 它能做什么？](/2017/03/15/Web-Audio-API-Guide-What-can-it-do/)
- **(2): 入门篇**
- [(3): 初级使用篇](/2017/03/15/Web-Audio-API-Guide-Primary-User/)
- [(4): 一些声音常识](/2017/03/15/Web-Audio-API-Guide-About-Sound/)
- [(5): 进阶篇](/2017/03/15/Web-Audio-API-Guide-Advanced-User/)

[【Gitbook 版本】](https://benzleung.gitbooks.io/web-audio-api-mini-guide/)

## 概念：AudioContext

AudioContext 是一个管理、播放声音的对象，大概跟 Canvas 的 context 差不多。

我们可以在 AudioContext 里“画”出各种声音。

## 初始化 AudioContext

<!-- more -->

```javascript
var AudioContext = window.AudioContext || window.webkitAudioContext;
var ctx = new AudioContext();
```

## 加载、解码mp3文件

```javascript
// 创建一个XMLHttpRequest请求对象
var request = new XMLHttpRequest();
// 请求一个MP3文件
request.open('GET', 'https://path/to/audio.mp3', true);
// Web Audio API 固定为 "arraybuffer"
request.responseType = 'arraybuffer';
// 加载完成后解码
request.onload = function() { 
    ctx.decodeAudioData(request.response, function(buffer) { 
        // 获得解码后的数据：buffer 
        // 这里可以立即播放解码后的 buffer，也可以把 buffer 值先存起来
        // 这个 playBuffer() 将在下文讲解如此实现
        playBuffer(buffer);
    }, function (){ 
        // 这里写解码出错的处理（例如文件损坏、格式不对等） 
    }); 
};
// 发送这个XMLHttpRequest请求
request.send();
```

1. 使用 `XMLHTTPRequest` 对象加载mp3资源数据
2. 加载完成后，mp3数据将被存储到 request.response 里
3. 使用 `ctx.decodeAudioData` 把mp3解码成wav格式的 `buffer`（异步）

## 播放声音

```javascript
function playBuffer(buffer) { 
  var sourceNode = ctx.createBufferSource(); 
  sourceNode.buffer = buffer; 
  sourceNode.connect(ctx.destination); 
  sourceNode.start(0); 
}
```

1. 加载mp3并解码得到 `buffer` 之后…
2. 创建 `sourceNode` 对象（下文将详解这个对象）；
3. 把 `buffer` 传入 `sourceNode.buffer` 属性；
4. 把 `sourceNode` 连接到 `AudioContext` 的音频输出口“`.destination`”；
5. 对 `sourceNode` 执行 `.start(0)`，声音就出来了。

![sourceNode 处理 buffer，然后传输到 destination](/web-audio-api-demo/guide-img/1/3.png "sourceNode 处理 buffer，然后传输到 destination")

## 小结：播放声音的基本步骤

1. 初始化 `AudioContext` 对象
1. 使用 `XMLHTTPRequest` 对象从服务器获取mp3
1. 把mp3数据用 `ctx.decodeAudioData` 转换成wav格式的 `buffer`
1. 使用 `SourceNode` 对象（下文将详解这个对象）把 `buffer` 装起来
1. 让 `SourceNode` 连接到音频输出口 `destination`
1. 执行 `.start(0)` 触发 `SourceNode` 开始播放声音

## 概念：SourceNode

**SourceNode** 是记录和输出波形声音的对象。

要使用 `AudioContext` 对象的 `createBufferSource` 方法创建，而不是 new。

```javascript
var sourceNode = ctx.createBufferSource();
```

它是其中一种 **AudioNode**（下文将详解）。

## 概念：AudioNode

这是一种由 AudioContext 生成的基本对象，用于**生成**或者**处理**音频波形数据。

AudioContext 提供很多特定的 AudioNode 可供使用，例如**均衡器、滤波器、振荡器**……

AudioNode 大体可分3类。

第一种：作为**【声音源头】**的 AudioNode，用于存储或生成声音（例如上文的 SourceNode）：

![作为**声音源头**的 AudioNode](/web-audio-api-demo/guide-img/1/4.png "作为声音源头的 AudioNode，用于存储或生成声音")

第二种：作为**【过滤器】**的 AudioNode，会对输入（In）的声音进行处理之后再输出（Out）。

（有些 AudioNode 可以有多个输入（In），然后对它们做混音后再输出。）

![作为**过滤器**的 AudioNode](/web-audio-api-demo/guide-img/1/5.png "作为过滤器的 AudioNode")


第三种：**【destination】** 是特殊的 AudioNode，它收到声音之后会直接传到耳机或扬声器，让我们最终听到声音。

![声音传入 **destination** 就能让我们最终听到声音](/web-audio-api-demo/guide-img/1/6.png "ctx.destination")

## 把 AudioNode 连接起来…

多个 AudioNode 连接起来，可得到最终的声音：

```javascript
sourceNode.connect(node1);
node1.connect(node2);
node2.connect(node3);
// ......
nodeN.connect(ctx.destination);
```

![多个 AudioNode 连接起来，可得到最终的声音](/web-audio-api-demo/guide-img/1/7.png "多个 AudioNode 连接起来，可得到最终的声音")

## 注意

一般来说，作为**【声音源头】**的 AudioNode （例如 SourceNode，以及将于进阶篇提及的 OscillatorNode），它的声音播放完毕后，它自身会立即销毁。

若需要再次播放，必须重新创建它。

例如，对于 SourceNode，每当重新播放同一个音频，**每次**都需要重新执行以下代码：

```javascript
function playBuffer(buffer) { 
  var sourceNode = ctx.createBufferSource(); 
  sourceNode.buffer = buffer; 
  sourceNode.connect(ctx.destination); 
  sourceNode.start(0); 
}
```

## 接下来...

下一节：[Web Audio API 小教程(3): 初级使用篇](/2017/03/15/Web-Audio-API-Guide-Primary-User/)

将对 SourceNode 做更多的控制，例如中途停止、循环播放、调节音量，并介绍如何实现多个 SourceNode 同时播放。

## 一个更好的选择

如果觉得 Web Audio API 写起来代码量太大，那么可以考虑使用我已经封装好的 Javascript 库 —— **Benz Audio Engine**。

项目 Github：[https://github.com/BenzLeung/benz-audio-engine](https://github.com/BenzLeung/benz-audio-engine)

**Benz Audio Engine** 只把 Web Audio API 常用功能做个简单的封装，实现音效的加载、播放、暂停、音量调节。简单、干净，没有多余的功能。

## 参考文献

- Web Audio API 简易入门教程，[http://newhtml.net/web-audio-api%E7%AE%80%E6%98%93%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B/](http://newhtml.net/web-audio-api%E7%AE%80%E6%98%93%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B/)
- W3C Editor’s Draft，[https://dvcs.w3.org/hg/audio/raw-file/tip/webaudio/specification.html](https://dvcs.w3.org/hg/audio/raw-file/tip/webaudio/specification.html)
