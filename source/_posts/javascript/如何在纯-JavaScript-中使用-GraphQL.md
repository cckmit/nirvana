---
title: 如何在纯 JavaScript 中使用GraphQL
date: 2021-03-11 21:12:40
categories: 前端
---

作者 | Brian Rinaldi译者 | 王强策划 | 李俊辰

除了 REST 以外，很多 API 都开始支持 GraphQL，甚至完全支持它了。但是，如果你需要使用一个 GraphQL API，你很自然就会想到自己需要使用 React 和 / 或其他一些库才能让它跑起来。这是因为许多教程和示例代码似乎都基于这样一个假设，也就是说如果你在使用 GraphQL，就需要使用这些库。

但是，一个对 GraphQL API 的查询只不过是一个定制格式的 HTTP 请求而已。一个 GraphQL 响应也不过是 JSON 罢了。你不需要动用什么精美的库也可以处理它们。在这篇教程中，我想采用一种不一样的方法，并向你展示从 Node 和客户端 JavaScript（不带库）调用 GraphQL API 实际上有多么容易。

GraphQL 请求的基础知识

与 RESTful API 不同，GraphQL 具有单个端点。尽管 GraphQL 可以响应 GET 请求，但是一个典型的 GraphQL HTTP 请求是作为一个 POST 请求发送的。

你的 GraphQL 请求可以发送三段数据：query、operationName 和 variables。

query 是必需的，并且包含了（你猜对了）GraphQL 查询。由于 GraphQL 是通过单个端点运行的，因此端点响应的数据完全取决于你的查询。这个查询需要正确格式化 GraphQL。想知道如何构建查询吗？请查阅关于如何编写 GraphQL 查询的教程。

https://stepzen.com/blog/how-to-write-graphql-queries

variables 是可选的，并且是一个 JSON 对象，其中包含要传递给查询的所有变量的值。例如，如果你的查询需要一个变量 id（将在查询中显示为 $id），则需要按以下方式发送变量：

```
{"id":1}
```

operationName 也是可选的。当查询包含多个命名操作的查询时，它用于指定要运行的操作。

如果你将一个 GraphQL 作为一个 GET 请求发送，则需要将以上内容作为查询参数传递。鉴于 GraphQL 查询可能会变得很长，这实际上不是最佳选择，因此我们还是考虑 POST 请求。在本教程中，我们将介绍我在 StepZen 上创建的一个简单的 Scooby DooAPI，它用来连接到一个 MySQL 数据源（StepZen 现在处于私有 alpha 状态，但是你可以在此处请求访问：

https://stepzen.com/request-invite

在 Node.js 中发送查询我们可以通过不带任何特殊库的 Node.js 发送查询，利用标准的 Node https 库格式化一个 POST 请求。我们来看一个不使用特殊库的简单示例（请注意，我确实使用了 dotenv 来获取用于访问我 StepZen 后端的 API 密钥）。在这个示例中，我仅传递了一个 query，该查询在发送前需要字符串化。除此之外，这是一个相当标准的 HTTPPOST。

```
const https = require('https');
require('dotenv').config();
const data = JSON.stringify({
  query: `{
    characters(isMonster:true) {
      name
      episode {
        name
      }
    }
  }`,
});
const options = {
  hostname: 'biggs.stepzen.net',
  path: '/scoobydoo/scoobydoo/__graphql',
  port: 443,
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': data.length,
    Authorization: 'Apikey ' + process.env.STEPZEN_API_KEY,
    'User-Agent': 'Node',
  },
};
const req = https.request(options, (res) => {
  let data = '';
  console.log(`statusCode: ${res.statusCode}`);
  res.on('data', (d) => {
    data += d;
  });
  res.on('end', () => {
    console.log(JSON.parse(data).data);
  });
});
req.on('error', (error) => {
  console.error(error);
});
req.write(data);
req.end();
```

同样，返回的数据只是 JSON 而已，因此如果我们要运行它，则控制台中的输出将是：

```
{
  characters: [
    { episode: [Object], name: 'Black Knight' },
    { episode: [Object], name: 'Ghost of Captain Cutler' },
    { episode: [Object], name: 'Phantom' },
    { episode: [Object], name: 'Miner Forty-Niner' }
  ]
}
```

简化请求
我们来使用 node-fetch 之类的东西来简化这一过程，以减少发出 HTTP 请求所需的样板代码量。node-fetch 库从 Node 中的浏览器实现 JavaScript fetch API。这样我们就可以丢弃大约 11 行代码（减少了 25％），同时还让代码更容易阅读了。

```
const fetch = require('node-fetch');
require('dotenv').config();
async function getData() {
  const data = JSON.stringify({
    query: `{
        characters(isMonster:true) {
          name
          episode {
            name
          }
        }
      }`,
  });
  const response = await fetch(
    'https://biggs.stepzen.net/scoobydoo/scoobydoo/__graphql',
    {
      method: 'post',
      body: data,
      headers: {
        'Content-Type': 'application/json',
        'Content-Length': data.length,
        Authorization: 'Apikey ' + process.env.STEPZEN_API_KEY,
        'User-Agent': 'Node',
      },
    }
  );
  const json = await response.json();
  console.log(json.data);
}
getData();
```

运行上面代码的结果与我们之前的示例相同。

传递变量在此示例中，我们的查询具有一个需要传递的变量（$id）。为了传递这个变量，我们需要将变量值添加到请求正文中包含的数据里。这应该是一个 JSON 格式的列表，其中查询所需的每个变量在 JSON 中都有一个对应的值。

```
const fetch = require('node-fetch');
require('dotenv').config();
async function getData(id) {
  const data = JSON.stringify({
    query: `query ScoobyDooWhereAreYou($id: ID!) {
        character(id:$id) {
          name
          isMonster
        }
      }`,
    variables: `{
        "id": "${id}"
      }`,
  });
  const response = await fetch(
    'https://biggs.stepzen.net/scoobydoo/scoobydoo/__graphql',
    {
      method: 'post',
      body: data,
      headers: {
        'Content-Type': 'application/json',
        'Content-Length': data.length,
        Authorization: 'Apikey ' + process.env.STEPZEN_API_KEY,
        'User-Agent': 'Node',
      },
    }
  );
  const json = await response.json();
  console.log(json.data);
}
getData(1);
```

在本例中，我传递的 ID 值为 1，它返回的是 Scooby Doo，这不是什么巧合：

```
{ character: { isMonster: false, name: 'Scooby Doo' } }
```

现在我们知道 Scooby Doo 在哪里了。

使用客户端 JavaScript 发送查询

通过客户端 JavaScript 调用 GraphQL 查询的操作与上面的 fetch 示例几乎是一样的，只有一些很小的区别。首先，我显然不需要导入一个库来支持 fetch。其次，更重要的是我无法访问环境变量。需要强调的是，如果你的 API 需要传递某种 API 密钥或凭据，那么你不会希望在客户端执行这一操作，因为你的凭据将被公开。更好的解决方案是调用一个可访问这些凭据的无服务器函数，然后为你调用 API 并返回结果。如果你的无服务器函数是用 JavaScript 编写的，则前面示例中的 Node 代码就会起作用。不过在 API 完全开放的情况下，我们先来看一下它是如何完成的（请注意，我的示例确实有一个 API 密钥，但请按照我说的那样做，不要像在演示中那样对付一下……）。

以下示例调用了我的 Scooby API 来获取一个 monster 列表和它们所出现的剧集列表（抱歉，Scooby 粉丝们，我现在只统计了第 1 季中的少数 monster）。然后它会获取结果并将其显示在浏览器中。尽管这对 GraphQL 调用来说并不重要，但我使用 js-beautify 正确格式化了要显示的 JSON 结果，然后使用 Prism 给它上了色。

```
<html>
  <head>
    <title>GraphQL Query Example</title>
    <link href="css/prism.css" rel="stylesheet" />
  </head>
  <body>
    <pre><code class="language-json" id="code"></code></pre>
    <script src="js/prism.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/js-beautify/1.13.0/beautify.js"></script>
    <script>
      (async function () {
        const data = JSON.stringify({
          query: `{
    characters(isMonster:true) {
      name
      episode {
        name
      }
    }
  }`,
        });
        const response = await fetch(
          'https://biggs.stepzen.net/scoobydoo/scoobydoo/__graphql',
          {
            method: 'post',
            body: data,
            headers: {
              'Content-Type': 'application/json',
              'Content-Length': data.length,
              Authorization:
                'Apikey DONOTSENDAPIKEYS',
            },
          }
        );
        const json = await response.json();
        document.getElementById('code').innerHTML = js_beautify(
          JSON.stringify(json.data)
        );
        Prism.highlightAll();
      })();
    </script>
  </body>
</html>
```

运行以上代码的结果是 JSON 响应，其中包含浏览器中显示的角色和剧集数据。

![图片](https://upload-images.jianshu.io/upload_images/10024246-df2d216399539c3c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

显然，一般来说你不会想要简单地向用户显示查询结果，因此让我们看一下如何使用返回的数据。

使用 GraphQL 查询响应

GraphQL 的一大优点是，它的响应只是纯 JSON，因此数据使用起来很容易。这里更好的是响应对应了查询，这意味着你无需花费大量时间来分析关于响应的文档。因此我们可以快速采用上面的示例并利用返回的数据，而不是简单地把它摆出来。

下面的代码会获取 JSON 响应，然后将其转换为 HTML（使用模板字面量），以将各项附加到一个 HTML 列表上。

```
<ul id="monsterList"></ul>
<script>
  (async function () {
    const data = JSON.stringify({
      query: `{
    characters(isMonster:true) {
      name
      episode {
        name
      }
    }
  }`,
    });
    const response = await fetch(
      'https://biggs.stepzen.net/scoobydoo/scoobydoo/__graphql',
      {
        method: 'post',
        body: data,
        headers: {
          'Content-Type': 'application/json',
          'Content-Length': data.length,
          Authorization:
            'Apikey DONOTSENDAPIKEYS',
        },
      }
    );
    const characterData = await response.json();
    const templateFn = (name, episode) => `<li>${name} (${episode})</li>`;
    const monsterList = document.getElementById('monsterList');
    characterData.data.characters.map((character) => {
      monsterList.insertAdjacentHTML(
        'beforeend',
        templateFn(character.name, character.episode.name)
      );
    });
  })();
</script>
```

运行这个简单示例的输出是一个角色及其出现剧集的无序列表。

![图片](https://upload-images.jianshu.io/upload_images/10024246-79bde22afcf3e136?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来可以做什么

这里的目标不是让大家不要使用 GraphQL 客户端库来执行 GraphQL 查询。那些库提供的能力远远超过了我在这里讨论的简单功能。此外，许多工具具备很容易和前端框架（例如 React、Vue、Angular）集成的能力。但是，对于研究 GraphQL 的人们来说（尤其是将其与 REST 对比时），很重要的一点是使用 GraphQL 显然不需要任何外部依赖项。如果你有兴趣探索一些 JavaScript 库，请参考下面的这些流行选项：

*   Apollo Client

*   https://github.com/apollographql/apollo-client

*   urql

*   https://formidable.com/open-source/urql/

*   Relay

*   https://relay.dev/

*   GraphQL Request

*   https://github.com/prisma-labs/graphql-request

 作者介绍

Brian Rinaldi 是 StepZen 的一位开发倡导者。Brian 在 Progress Software 和 Adobe 等公司已有 10 多年的经验，一直致力于开发者社区和开发者关系工作。Brian 加入软件开发行业已超过 20 年，主要研究 Web 前端和后端技术。

 延伸阅读

https://stepzen.com/blog/consume-graphql-in-javascript
