---
layout: post
title: 什么是JSONP? 实现JSONP
categories: [js]
---

# JSONP

1. 什么是JSONP
  
  - JSONP（JSON with Padding）是JSON的一种”使用模式“，可用于解决主流浏览器的跨域数据访问的问题。
2. JSONP的实现原理
  
  - 由于浏览器同源策略限制，网页无法通过Ajax请求非同源的接口数据。
  - script标签不受浏览器同源策略的影响，可以通过src属性，请求非同源的js脚本数据。
  - 通过函数调用的形式，接收跨域接口响应回来的数据。

### index.html

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>jsonp-test</title>

    <script
      src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.7.1/jquery.min.js"
      integrity="sha512-v2CJ7UaYy4JwqLDIrZUI/4hqeoQieOmAZNXBeQyjo21dadnwR+8ZaIJVT8EE2iyI61OV8e6M8PP2/4hpQINQ/g=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    ></script>
  </head>
  <body>
    jsonp-test

    <script>
      // ajax 请求
      // $(function () {
      //   $.ajax({
      //     type: "get",
      //     url: "http://localhost:3000/test",
      //     success: (data) => {
      //       console.log(data, "ajax请求");
      //     },
      //   });
      // });

      

      // 后端 无 jsonp的处理
      // function jsonpCallbackFn(data) {
      //   console.log(data, "回调拿到结果");
      // }

      // var script = document.createElement("script");
      // script.src = "http://localhost:3000/test?cbFnName=jsonpCallbackFn";
      // document.head.append(script);

      // jsonp
      // function jsonpCallbackFn(data) {
      //   console.log(data, "回调拿到结果");
      // }

      // var script = document.createElement("script");
      // script.src = "http://localhost:3000/jsonp?cbFnName=jsonpCallbackFn";
      // document.head.append(script);

      // jq 简化jsop操作
      $.ajax({
        url: "http://localhost:3000/jsonp",
        type: "get",
        dataType: "jsonp",
        jsonp: "cbFnName",
        success: function (data) {
          console.log(data, "回调拿到结果-jq 简化jsop操作");
        },
      });
    </script>
  </body>
</html>

```

### index.js

```js
const express = require("express");
const app = express();
const port = 3000;

app.get("/test", (req, res) => {
  console.log(req.query, "test-参数");

  res.send({
    test: "测试jsonp",
  });
});

app.get("/jsonp", (req, res) => {
  console.log(req.query,req.query.cbFnName, "jsonp-参数");

  let sendData = {
    test: "测试jsonp",
  };
  let sendStr = `
    ${req.query.cbFnName}(${JSON.stringify(sendData)})
  `;
  res.send(sendStr);
});

app.listen(port, () => {
  console.log(`Example app listening on port ${port}. http://localhost:3000`);
});

```

### package.json

```json
{
  "name": "jsonp-test",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2"
  }
}

```

### 开始

```shell
npm i && npm run dev
```
