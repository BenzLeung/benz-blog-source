---
title: 记录一些物理学术语和翻译
date: 2017-02-17 11:18:04
tags: chipmunk.js
---

同步更新：[Github Wiki](https://github.com/BenzLeung/cocos2d-js-demo/wiki/%E7%89%A9%E7%90%86%E5%AD%A6%E4%B8%93%E4%B8%9A%E6%9C%AF%E8%AF%AD%E6%94%B6%E9%9B%86)

我在学习 chipmunk 物理引擎的过程中，感觉到了明显的困难。估计很大可能是受限于我的英文水平和物理知识了。于是特开此帖，记录学习过程中遇到的物理专业术语。内容不断更新。

* segment 线段
* box 矩形
* circle 圆形
* polygon （通常缩写 poly） 多边形
* moment 转动惯量
* body 物体
* shape 形状
* vector （通常缩写 vect） 向量、矢量，在 chipmunk 中还用来表示点的坐标（因为向量和坐标都是x和y）
* vertex （通常缩写 vert） 顶点（多边形的），我已华丽丽地与 vect 混淆
* area 面积
* elasticity 弹性、弹性系数
* friction 摩擦力、摩擦系数

<!-- more -->

***

### segment 线段

在 chipmunk 中，一般使用线段来建造墙壁。

线段也可以有面积，chipmunk 提供一个 function ：

```javascript
/**
 * @param {cp.Vect} a 顶点坐标
 * @param {cp.Vect} b 另一顶点坐标
 * @param {Number} r 线段粗细的一半（半径）
 */
cp.areaForSegment = function(a, b, r) {
	return r*(Math.PI*r + 2*vdist(a, b));
};
```

以上代码是从 chipmunk 的源代码取出来的（代码里的注释是我加的）。从源代码看，chipmunk 把线段的两端当成了圆，所以 r 是两端的圆的半径。

### moment 转动惯量

[https://zh.wikipedia.org/wiki/%E8%BD%89%E5%8B%95%E6%85%A3%E9%87%8F]()

[https://www.zhihu.com/question/24218339]()

转动惯量是一个物体对于其旋转运动的惯性大小的量度。

chipmunk 在创建物体（Body）的时候需要指定转动惯量。

```javascript
/**
 * @param {Number} m 质量
 * @param {Number} i 转动惯量
 */
var x = new cp.Body(m, i);
```

chipmunk 提供了计算物体转动惯量的 function，一般而言套用公式就好了。

圆形/圆环的转动惯量：

```javascript
/**
 * @param {Number} m 质量
 * @param {Number} r1 内环的半径，如果是实心圆，请指定为 0
 * @param {Number} r2 外环的半径
 * @param {cp.Vect} offset 重心的偏移向量，可以把圆的重心弄偏。
 *      若不需要弄偏重心，请指定为 cp.vzero （零向量）
 * @return {Number} 转动惯量
 */
var i = cp.momentForCircle(m, r1, r2, offset);
```

矩形的转动惯量：

```javascript
/**
 * @param {Number} m 质量
 * @param {Number} width 矩形的宽
 * @param {Number} height 矩形的高
 * @return {Number} 转动惯量
 */
var i = cp.momentForBox(m, width, height);
```

线段的转动惯量：

```javascript
/**
 * @param {Number} m 质量
 * @param {cp.Vect} a 顶点坐标
 * @param {cp.Vect} b 另一顶点坐标
 * @return {Number} 转动惯量
 */
var i = cp.momentForSegment(m, a, b);
```

多边形的转动惯量：

```javascript
/**
 * @param {Number} m 质量
 * @param {Array.<cp.Vect>} verts 顶点坐标数组，数组至少包含3个值
 * @param {cp.Vect} offset 重心的偏移量，chipmunk 会自动计算多边形的
 *      质心（即无偏移的重心），这个值可以把正常的重心给弄偏。若不需要
 *      弄偏重心，请指定为 cp.vzero （零向量）
 * @return {Number} 转动惯量
 */
var i = cp.momentForPoly(m, verts, offset);
```
todo: 尚未清楚如果不套用公式，自己指定转动惯量的话，会有些什么有趣的效果。

### vector 向量、矢量

在 chipmunk 中，向量是个类/对象。

```javascript
/**
 * @param {Number} x 横向值
 * @param {Number} y 纵向值
 */
var v = new cp.Vect(x, y);
```

由于**坐标点**和向量都是 x 和 y 值，所以 chipmunk 也把这个向量用作坐标点的表示。




***

持续更新中...
