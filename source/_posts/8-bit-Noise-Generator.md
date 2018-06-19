---
title: 8位风格噪波生成器
date: 2017-03-27 17:32:43
tags: [Web Audio API,Javascript]
---

基于 Web Audio API，我做了一个js库，用来模拟任天堂红白机或者 Game Boy 的噪波音轨。

- 代码：[https://github.com/BenzLeung/8bitNoise.js](https://github.com/BenzLeung/8bitNoise.js)
- 一个Demo：[https://benzleung.github.io/8bitNoise.js/demo.html](https://benzleung.github.io/8bitNoise.js/demo.html)

任天堂红白机的声音模块包含5个基本音轨：2个方形波、1个三角波、1个噪波、1个任意波音轨。Game Boy 和 Game Boy Color 有4个音轨，比红白机少了1个任意波音轨。

其中方形波和三角波可以用 Web Audio API 的 Oscillator （振荡器）模拟，而噪波需要另想它法。

一直想用纯粹的 Web Audio API 演奏红白机和 Game Boy 的经典背景音乐，但是在噪波音轨上遇到了困难。

搜遍了github也没有人用 Web Audio API 成功模拟出这种8位风格的噪波的，多数人都只使用白噪声、粉噪声、褐噪声来做噪波。然而8位游戏机所用的并不是普通的噪声。

于是我查了大量英文资料，并用模拟器录一段噪波做研究，发现要模拟8位噪波还挺简单的。

水到渠成，三下五除二，现在写好了。

接下来我要弄一些乐谱来演奏咯~
