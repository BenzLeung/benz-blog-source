---
title: 【译】 在 React 下发起 AJAX 请求的5种方案
date: 2017-07-07 22:37:41
tags:
---

这是我首次翻译别人的文章。最近我在学习 React，发现它不提供 AJAX 功能。后来我发现了这一篇文章，觉得写得不错，简洁实用，于是翻译出来。

-------------------

- 原文：[5 best libraries for making AJAX calls in React](https://hashnode.com/post/5-best-libraries-for-making-ajax-calls-in-react-cis8x5f7k0jl7th53z68s41k1)
- 原作者：[Sandeep Panda](https://hashnode.com/@sandeep)
- 译者：Benz（就是我自己咯~）

--------------------

近两年我一直在使用 React。也许很多初学者*（Benz乱入：例如我呀）*会有一个疑问：”React 是如何从服务器获取数据的？“或者”在 React 下如何发起 AJAX 请求？“要回答这个问题，也许一些大牛会告诉你，React 只是一个 view 层的JS库，不提供 AJAX 功能，你可以自由地选择你以前最喜欢的方式来实现 AJAX。然而，这个答案对我们的帮助不大——尤其是目前 JavaScript 领域的发展如此迅速*（Benz乱入：甚至发展得有点乱糟糟的）*。因此，我将在这篇文章试着回答这个基础问题，并列出5个可以在 React 实现 AJAX 的比较简洁的JS库。

<!-- more -->

### jQuery $.ajax

这是一个快速又粗暴的方案。在旧版本的[官方 React 教程（official React tutorial）](https://chenglou.github.io/react/docs/tutorial.html)中，他们使用了 jQuery `$.ajax` 来示范如何从服务器获取数据。如果你是刚刚开始学习和把玩 React，jQuery 可以节省你大量入门和开发的时间，因为我们都对 jQuery 非常熟悉了。这是 jQuery 实现 AJAX 的例子：

```javascript
loadCommentsFromServer: function() {
    $.ajax({
        url: this.props.url,
        dataType: 'json',
        cache: false,
        success: function(data) {
            this.setState({data: data});   // 注意这里
        }.bind(this),
        error: function(xhr, status, err) {
            console.error(this.props.url, status, err.toString());
        }.bind(this)
    });
}
```

*P.S. 这代码段摘自旧版本的[官方教程](https://chenglou.github.io/react/docs/tutorial.html)*

这里演示了如何在一个 React 组件里面使用 jQuery 的 `$.ajax`。唯一需要注意的是如何在 `success` 回调里面调用 `this.setState()` ，即当 jQuery 成功收到数据之后应该如何通过 React 的 API 更新 state 的。

然而，jQuery 是一个包含很多功能的大头儿，只为了用一下 AJAX 功能而引入整个 jQuery 是没有意义的（除非你还使用 jQuery 做了很多别的事情）。So，用什么才好？答案是 `fetch` API。


### Fetch API

`Fetch` 是个新的、简单的、标准化的API，旨在统一Web通信机制，并替代 `XMLHttpRequest`。它已经被主流浏览器所支持，针对较旧的浏览器也有了一个 [polyfill](https://github.com/github/fetch)  *（Benz乱入：polyfill 直译是填充工具，就是旧浏览器本来不支持某个新的JS API，引入一段js代码后就支持了，这一段js代码给旧浏览器”填充“了一个API。这个单词我实在不知道怎么翻译 ，感觉反而保留原单词不翻译更能让读者理解。）*。如果你在使用 Node.js ，你也可以通过 [node-fetch](https://github.com/bitinn/node-fetch) 来使 Node.js 支持 `Fetch`。

若把上述用 jQuery `$.ajax` 的代码段改成 `fetch` 实现的话，代码应该长这样子：

```javascript
loadCommentsFromServer: function() {
    fetch(this.props.url).then(function(response){
        // 在这儿实现 setState
    });
}
```

在一些流行的 React 教程中你也许会发现 `fetch` 的身影。要了解更多关于 `fetch` 的情况，可参考下列链接（全英文）：

- [Mozilla](https://developer.mozilla.org/en/docs/Web/API/Fetch_API)
- [David Walsh Blog](https://davidwalsh.name/fetch)
- [Google Developers](https://developers.google.com/web/updates/2015/03/introduction-to-fetch?hl=en)
- [WHATWG](https://fetch.spec.whatwg.org/)


### SuperAgent

[SuperAgent](https://visionmedia.github.io/superagent/) 是一个轻量级的 AJAX API 库，为更好的可读性和灵活性而生。如果某些原因让你不太想用 `fetch`，那么 SuperAgent 就几乎是必然的选择了。SuperAgent 的用法大概是这样的：

```javascript
loadCommentsFromServer: function() {
    request.get(this.props.url).end(function(err,res){
        // 在这儿实现 setState
    });
}
```

SuperAgent 也有 Node.js 版本，API 是一样的。如果你在用 Node.js 和 React 开发[同构应用](http://isomorphic.net/javascript)*（Benz 乱入：这个链接是我加的，旨在照顾初学者）*，你可以用 webpack 之类的东西嵌入 `superagent` 并让它适用于浏览器端。因为浏览器端和服务器端的 API 是一样的，所以其 Node.js 版本不需要修改任何代码就可以在浏览器上运行。


### Axios

[Axios](https://github.com/mzabriskie/axios) 是一个基于 [promise 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)*（Benz 乱入：这个链接也是我加的）*的 HTTP 客户端。与 `fetch` 和 `superagent` 一样，它同时支持浏览器端和 Node.js 端。另外你可以在其 Github 主页上发现，它有很多很实用的高级功能。

这是 Axios 的大概用法：

```javascript
loadCommentsFromServer: function() {
    axios.get(this.props.url).then(function(response){
        // 在这儿实现 setState
    }).catch(function(error){
        // 处理请求出错的情况
    });
}
```


### Request

若不介绍这个 [request](https://github.com/request/request) 库，感觉上本文会不太完整。这是一个在思想上追求极简设计的JS库，在 Github 上拥有超过 12k 的 star *（Benz 乱入：我翻译这文章时已经 16k+ star 了）*。它也是最流行的 Node.js 模块之一。进入它的 [GitHub 主页](https://github.com/request/request) 了解更多。

用法示例：

```javascript
loadCommentsFromServer: function() {
    request(this.props.url, function(err, response, body){
        // 在这儿实现 setState
    });
}
```


## 我的选择

因为 `fetch` 是新的标准化的API，所以，在任何需要 AJAX 的地方（不论在 React 里或是其他所有 JS 应用），我都更倾向于使用 `fetch`。
