---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useImperativeHandle 與 React.forwardRef'
date: '2021-10-12'
tags: ['React', 'JavaScript']
draft: false
summary: '在 React hooks 中 useImperativeHandle 是一個相對較少使用的 hook，且使用它的時候也必須搭配著 React.forwardRef 來使用。如果是剛開始學 React 或是從來都沒有仔細看手冊的人，可能都不會知道有這個 hook 的存在，或是早就忘記它的存在。今天就讓我來瞭解瞭解 useImperativeHandle，這個冷門又容易被遺忘的 hook 吧！或許瞭解之後，可能在某些情境下可以考慮使用它。'
images: []
---
### 前言
在 React hooks 中 useImperativeHandle 是一個相對較少使用的 hook，且使用它的時候也必須搭配著 React.forwardRef 來使用。如果是剛開始學 React 或是從來都沒有仔細看手冊的人，可能都不會知道有這個 hook 的存在，或是早就忘記它的存在。今天就讓我來瞭解瞭解 useImperativeHandle，這個冷門又容易被遺忘的 hook 吧！或許瞭解之後，可能在某些情境下可以考慮使用它。


### React.forwardRef
在開始介紹 useImperativeHandle 之前，先來介紹它的好搭檔 forwardRef。forwardRef 與 memo 一樣是個 HOC (High order component) ，接受一個元件並回傳一個元件。

那為什麼需要 forwardRef 呢？直接來看下面的範例：

```javascript
function App() {
  const inputRef = React.useRef()

  return (
    <div>
      <input type="text" ref={inputRef} />
    </div>
  )

}

ReactDOM.render(<App />, document.getElementById('root'))
```
在這個範例中，我們使用了 useRef 並在 input ref 屬性掛上，以取得 input 的 DOM node。

那假設今天有種情況是需要將 input 抽象化成一個元件，並嘗試將 ref 透過 props 傳遞下去，掛在 input 的 ref 上，如下

```javascript
function InputField({ ref }) {
  return (
    <input type="text" ref={ref} />
  )
}

function App() {
  const inputRef = React.useRef()

  return (
    <div>
      <InputField ref={inputRef} />
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))
```
這個時候打開 console 就會發現 React 丟出的錯誤訊息

![](https://i.imgur.com/Xk3sRLK.png)

在 function component 中是不能夠將 ref 作為 props 傳遞的，這個時候 forwardRef 就登場啦！我們可以用 forwardRef 將元件包起來，他就會回傳一個可以傳遞 ref 的元件給我們。

```javascript=
const InputField = React.forwardRef((props, ref) => {
  return (
    <input type="text" ref={ref} />
  )
})
```
改成這樣子之後，我們就能夠順利取的 input DOM node 了！

### useImperativeHandle
介紹完 forwardRef 的基本使用方式之後，我們再來看看 useImperativeHandle 在 React 官方的說明以及基本的用法

> useImperativeHandle customizes the instance value that is exposed to parent components when using ref

```javascript
useImperativeHandle(ref, createHandle, [deps]))
```

簡單來說，我們可以將子元件定義的變數或是函式，透過 useImperativeHandle 給父層傳下來的 ref 定義屬性。讓我們直接看看使用範例：

```javascript
const InputField = React.forwardRef((props, ref) => {
  const inputRef = React.useRef()

  React.useImperativeHandle(
    ref,
    () => {
      return {
        focus: () => {
          inputRef.current.focus()
        }
      }
    })


  return (
    <input type="text" ref={inputRef} />
  )
})

function App() {
  const InputFieldRef = React.useRef()

  function clickHandler() {
    InputFieldRef.current.focus()
  }

  return (
    <div>
      <button onClick={clickHandler}>focus</button>
      <InputField ref={InputFieldRef} />
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))
```
在範例中，useImperativeHandle 第一個參數接收來自父層定義的 ref，然後我們在第二個 callback 參數必須回傳一個物件，而這個物件就會成為父層 ref 中 current 屬性的值。所以我們就在這個物件中定義了 focus 屬性，讓父層 ref 可以呼叫這個方法。

![](https://i.imgur.com/622fk9H.gif)


以上就是今天的分享，如果有任何問題都歡迎在下方留言！
