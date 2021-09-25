---
title: 'Re: 新手讓網頁 act 起來 - 永遠的起點 Hello world!'
date: '2021-09-17'
tags: ['React', 'JavaScript']
draft: false
summary: '在開始使用 React 之前，先來回想一下剛學習原生 JS 的時候，我們是怎麼在頁面產生 DOM Node，然後渲染出 Hello world：'
images: []
---
在開始使用 React 之前，先來回想一下剛學習原生 JS 的時候，我們是怎麼在頁面產生 DOM Node，然後渲染出 Hello world：

## 原生 DOM 的操作
假設我們要在一個頁面動態產生一個 div ，我們會這樣做：
```html
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>

  <script>
    const rootEl = document.createElement('div')
    rootEl.setAttribute('id', 'root')
    document.body.append(rootEl)

    const titleEl = document.createElement('div')
    titleEl.textContent = 'Hello world'
    titleEl.className = 'container'
    rootEl.append(titleEl)
  </script>
</body>
</html>
```
順利的話我們就能夠在畫面上看到 Hello world，如下


接下來，就讓我來使用 React 提供的 api。首先，我們要引入 React 以及 ReactDOM。

為什麼需要引入 React 以外還需要 ReactDOM 呢？ React 主要負責建立 UI 介面，但它其實並沒有與瀏覽器的 DOM 打交道，所以我們需要透過 ReactDOM 這個橋樑來與瀏覽器做交流。

在 React 中，第一個要學習的就是 creatElement()。這個 api 主要負責幫我們建立 React element，它可以接收至少三個參數。第一個可以是 HTML element，或是 component，第二個參數是 props，第三個參數之後都會被收集成 children，最後會回傳一個 React element。簡單一點，可以把它一點像 document.creatElemnt()。在後面的文章我們會再多介紹它。

再來就是 ReactDOM.render()，它接收兩個參數，第一個是我們的 React element，第二個是 DOM Node，功能就是負責將我們製造出的 React element 渲染在 DOM Node 裡。有一點像原生 append() 的感覺。

接下來就讓我們實際使用看看：

```html
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>

  <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>

  <script>
    const rootEl = document.createElement('div')
    document.body.append(rootEl)

    const element = React.createElement('div', {
      children: 'Hello React!',
      className: 'container'
    })

    ReactDOM.render(element, rootEl)
  </script>
</body>
</html>
```
順利的話，我們就可在瀏覽器順利看到 Hello React! 了！
![](https://i.imgur.com/VxOyzad.png)

以上就是今天的內容啦～，有什麼問題都歡迎在下方留言，明天將更近一步的了解 React.creatElement()。
