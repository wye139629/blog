---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useLayoutEffect'
date: '2021-10-11'
tags: ['React', 'JavaScript']
draft: false
summary: '在之前我們介紹過 useEffect hook ，今天介紹的 useLayoutEffect 其實大部分的功能跟 useEffect 一樣，我可以將 side effect 放進這兩 個 hook 做處理，但為什麼已經有了 useEffect 還要有個 useLayoutEffect 呢？ 那些微的差距是什麼？今天就讓我們來了解一下 useLayoutEffect 吧！'
images: []
---
### 前言
在之前我們介紹過 useEffect hook ，今天介紹的 useLayoutEffect 其實大部分的功能跟 useEffect 一樣，我可以將 side effect 放進這兩 個 hook 做處理，但為什麼已經有了 useEffect 還要有個 useLayoutEffect 呢？ 那些微的差距是什麼？今天就讓我們來了解一下 useLayoutEffect 吧！

### useLayoutEffect

首先，還是先介紹 useLayoutEffect 的基本使用方式，它跟 useEffect 一樣接收兩個參數：

1. callback
2. dependency array

```javascript
useLayoutEffect(() => {
  // side effect
},[])
```

與 useEffect 最主要的差異：

1. useLayoutEffect 的執行時間是在瀏覽器渲染畫面之前。
  所以整個 flow 會是：
      1. 執行 Lazy initialize
      2. React render
      3. React 更新 DOM
      4. 執行 useLayoutEffect
      5. 瀏覽器渲染畫面
      6. 執行 useEffect
2. useLayoutEffect 是同步執行，所以一定會等到 useLayoutEffect 的 callback 執行完，瀏覽器才會渲染畫面。

接下來就讓我們來看看使用它的時機吧！

### 範例

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .container {
      position: relative;
    }

    .box {
      width: 100px;
      height: 100px;
      background: black;
      position: absolute;
    }
  </style>
</head>

<body>
  <div id='root'></div>
  <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>

  <script type='text/babel'>
    const { useLayoutEffect, useEffect } = React
    function App() {
      const boxContainer = React.useRef(null)

      useEffect(() => {
        boxContainer.current.style.top = '200px'
      }, [])

      return (
        <div className='container'>
          <div className='box' ref={boxContainer}>
          </div>
        </div>
      )
    }

    ReactDOM.render(<App />, document.getElementById('root'))

  </script>
</body>

</html>

```
上面的範例，假設我們有一個 div 需要在 render 之後改變它的位置。在這邊我們先使用了 useEffect 搭配 useRef ，去改變 box 的 style。如果你打開畫面重新整理之後，會看到這樣閃一下的情況。

![](https://i.imgur.com/7FjhyEJ.gif)


這是因為 useEffect 的執行順序是在瀏覽器渲染之後，所以一開始的 box在 top 0px 的位置會被渲染出來，之後因為執行 useEffect 才移動到設定的位置。這個時候就是 useLayoutEffect 的出場時機啦！

當我們將 useEffect 改成 useLayoutEffect ，這個問題就會改善了！
所以當我們某些時候要改變一些 DOM 元素的尺寸、大小和顏色，可以考慮使用 useLayoutEffect 去做。但大部分情況其實使用 useEffect 就可以了！

以上就是今天的分享，如果有任何問題都歡迎在下方留言！
