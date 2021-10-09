---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useMemo'
date: '2021-10-09'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天我們介紹過如何使用 React.memo 與 useCallback 來做效能優化，而 useMemo 這個 hook 一樣也是用來優化效能的，今天就來介紹 useMemo 的基本使用方式吧！'
images: []
---
### 前言
昨天我們介紹過如何使用 React.memo 與 useCallback 來做效能優化，而 useMemo 這個 hook 一樣也是用來優化效能的，今天就來介紹 useMemo 的基本使用方式吧！

### useMemo
首先，剛接觸 React 時，看到 memo 與 useMemo 有時一定會搞混。memo 是一個 HOC (Hight order component) ，接收一個元件並且回傳一個元件。而 useMemo 是 React hooks 的其中一個 hook ，與 useCallback 類似，接收兩個參數

1. callback 並且回傳一個值
2. dependency array

```javascript
const memorizeValue = useMemo(() => computeExpensiveValue(a, b), [a, b])
```

useMemo 會將第一個傳進去的 callback return 的 value 記住，然後當 dependecny 有變動時，才會重新呼叫 callback 並 return 新的值。

看到這邊可以發現，它跟 useCallback 實在太像了！useCallback 是會紀錄函式，而 useMemo 是紀錄值。所以，以下的等式是成立的！

```javascript
useCallback(fn, []) = useMemo(() => fn, [])
```

雖然 useMemo 能夠取代 useCallback ，但透過不同名稱的 api 能夠聚焦在不同的情境上。

接下來就來看看如何使用 useMemo 吧！

```javascript
function computeExpensiveValue(number) {
  console.log('execute expensive function')
  return number <= 0 ? 1 : number * fibonacci(number - 1);
}

function App() {
  const [number, setNumber] = React.useState(10)
  const [click, setClick] = React.useState(0)
  const result = computeExpensiveValue(number)

  function onChangeHandler(e) {
    setNumber(Number(e.target.value))
  }

  return (
    <div>
      <input type="text" onChange={onChangeHandler} value={number} />
      {result}
      <button onClick={() => setClick(click + 1)}>{click}</button>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))
```
上面範例中，我們定義了遞迴函式，當我們給的數字越大，執行的次數會隨之增加。當我們按下 button 改變 click 的 state 觸法元件 re-render 這個函式都會重新執行。

![](https://i.imgur.com/KJNHe2A.gif)

但其實我們希望這個遞迴函式，只要在數字改變的時候再做計算，其他的 state 改變都應改與它無關，所以這個時候就可以使用 useMemo 解決這個問題。

```javascript
const result = React.useMemo(() => fibonacci(number), [number])
```
當我們使用 useMemo 紀錄這個 result ，只要 number 的 state 不改變，就算元件 re-render ，也都不會重新執行遞迴函式。

![](https://i.imgur.com/iwBAJwQ.gif)

以上就是關於 useMemo 的基本介紹與使用方式，如果有任何問題都歡迎在下方留言！
