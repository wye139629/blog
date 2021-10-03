---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useRef'
date: '2021-10-03'
tags: ['React', 'JavaScript']
draft: false
summary: '探索完 useState 與 useEffect ，今天就讓我們回來繼續介紹其他的 React hooks 吧！ 當我們剛開始使用 React 的時候，相信有不少人都會有個感覺，就是不用為了改變一個區塊的 state 一再的抓取 DOM node ，然後修改 DOM node 的 value。在 React 中，我們只要定義好元件與 state，讓 React 幫我們更新狀態。但或許在某些操作上，我們還是必須獲得 DOM node ，這個時候我們就能夠使用 useRef 來達成！接下來，就先讓我們來介紹 useRef 的使用方式吧！'
images: []
---
### 前言

探索完 useState 與 useEffect ，今天就讓我們回來繼續介紹其他的 React hooks 吧！ 當我們剛開始使用 React 的時候，相信有不少人都會有個感覺，就是不用為了改變一個區塊的 state 一再的抓取 DOM node ，然後修改 DOM node 的 value。在 React 中，我們只要定義好元件與 state，讓 React 幫我們更新狀態。但或許在某些操作上，我們還是必須獲得 DOM node ，這個時候我們就能夠使用 useRef 來達成！接下來，就先讓我們來介紹 useRef 的使用方式吧！

### useRef
與 useState 和 useEffect 相比，useRef 的使用方式簡單許多。 useRef 接收一個任意型別的 initialValue 作為初始值，然後會回傳一個 mutable object 。

```javascript
const refContainer = useRef(initialValue)
```

refContainer 這個物件中會含有 current 屬性，而值就會是一開始傳進去的 initialValue。可以把它想像成一個含有 current 屬性的盒子讓你存放任何值。而且，當每次元件觸發 re-render 重新呼叫 useRef 時，都會回傳同一個盒子回來。

再來，既然這個物件是可變動的，所以我們隨時可以變更這個物件

```javascript
refContainer.current = newValue
```

有特別一點要注意的是，當我們變更 refContainer object 時，並不會像 setState 一樣，觸發元件 re-render。

接下來讓我們實際練習看看怎麼使用它吧！

### To top 與 To bottom

來看看範例：
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<style>
  .box {
    width: 300px;
    height: 300px;
    overflow: auto;
    padding: 10px;
    border: 1px solid black;
  }
</style>

<body>

  <div id='root'></div>
  <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>

  <script type='text/babel'>

    function App() {

      return (
        <div>
          <button>To top</button>
          <button>To bottom</button>
          <p className='box'>
            Lorem ipsum dolor sit amet, consectetur adipisicing elit. Nobis aspernatur blanditiis quo culpa molestias harum minus quasi ab! Labore illum, distinctio ratione expedita ipsum nulla sequi totam fuga voluptas esse!
            Ipsa enim ex ut nesciunt non corrupti deleniti, inventore reiciendis esse velit. Maxime quisquam ipsa consequuntur aut expedita beatae tempora dicta quidem excepturi. Dolorem ut odit repellat, porro labore nostrum.
            Laudantium sapiente, tempora voluptatibus culpa cupiditate doloremque sed. Quidem quis eius, aspernatur atque hic quo eligendi inventore saepe facilis, tempora quaerat officiis enim distinctio quas adipisci nisi deleniti, minima eveniet?
            Aspernatur cupiditate ipsum error officiis possimus necessitatibus, ut ratione quibusdam iste impedit ad fugiat voluptas exercitationem sit. Molestias, quaerat. Eveniet assumenda omnis ipsam ea doloremque. Consectetur accusamus modi magnam possimus.
            Repellat aliquam at nesciunt. Fugit modi maxime, aspernatur qui accusamus sapiente repellendus temporibus suscipit! Debitis nostrum aperiam eos, fugit quae soluta libero, pariatur praesentium excepturi incidunt unde, non facilis voluptate.
          </p>
        </div>
      )
    }


    ReactDOM.render(<App />, document.getElementById('root'))
  </script>


</body>

</html>

```

![](https://i.imgur.com/ZknGyCP.png)

這個範例中，當我們點選 To top 與 To bottom 按鈕時，下方的捲軸會分別到最頂端與最底端。

1. 使用 useRef 建立一個 mutable 物件，並且在 box 的 ref 屬性掛上 mutable 物件 ，讓我們可以獲取 box DOM node

```javascript
const boxContainer = React.useRef(null)
```

```html
<p className='box' ref={boxContainer}></p>
```

2. 建立點擊事件，並透過 boxContainer.current 取得 box DOM node 然後更改 scrollTop

```javascript
const toTop = () => {
  boxContainer.current.scrollTop = 0
}

const toBottom = () => {
  const { current: boxEl } = boxContainer
  boxEl.scrollTop = boxEl.scrollHeight
}
```

```html
<button onClick={toTop}>To top</button>
<button onClick={toBottom}>To bottom</button>
```

這樣就子就完成啦！順利的話，當點選 To top 與 To bottom 按鈕時，下方的捲軸會分別到最頂端與最底端。

![](https://i.imgur.com/XkYWH3d.gif)

以上就是 useRef 的介紹，如果有任何問題都歡迎在下方留言～～
