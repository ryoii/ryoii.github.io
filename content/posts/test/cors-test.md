---
title: "偷懒的Cors测试"
date: 2022-03-15T10:42:26+08:00
lastmod: 2022-03-15T10:42:26+08:00
draft: false
authors: ['ryoii']
description: ""

tags: [测试]
---

最近被反馈开发的接口出现跨域问题，本着“人类本质”的精神，不想独立部署一个跨域的前端去测试跨域问题。
想到可以通过控制台模拟跨域情景，果断动手操作

```js
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://localhost:9999/test");
xhr.send(null);
xhr.onload = function(e) {
    var xhr = e.target;
    console.log(xhr.responseText);
}
```
