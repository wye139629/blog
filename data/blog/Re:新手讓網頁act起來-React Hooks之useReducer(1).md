---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useReducer(1)'
date: '2021-10-04'
tags: ['React', 'JavaScript']
draft: false
summary: '如果有接觸過 Redux 的話，應該會對這個 hook 有親切感。如果是像我一樣沒有接觸的話也沒關係，其實可以把 useReducer 想像成另一種 useState，從這個角度去學習會比較好理解，那今天就讓我來瞭解一下怎麼使用 useReducer 吧！'
images: []
---
### 前言
如果有接觸過 Redux 的話，應該會對這個 hook 有親切感。如果是像我一樣沒有接觸的話也沒關係，其實可以把 useReducer 想像成另一種 useState，從這個角度去學習會比較好理解，那今天就讓我來瞭解一下怎麼使用 useReducer 吧！

### useReducer

useReducer 可以接收三個參數：
1. 一個 callback
2. 初始值
3. lazy initialize callback

並且回傳一個兩個值的陣列，第一個值跟 useState 一樣，就是會拿到 initialValue，而第二個值是一個函式，可以把先把它想像成是另一種的 setState，所以寫起來會是長這個樣子：

```javascript
const [state, setState] = useReducer(()=>{}, initialValue, ()=>{})
```
那第一個 callback 到底要寫些什麼呢？慣例上會稱它叫 reducer，那它會接收兩個參數，第一個是當下的 state ，第二個值會是當我們使用 useReducer 回傳的 setState ，呼叫時傳進去的參數。例如說：

```javascript=
const [state, setState] = useReducer((state, newState)=>{
console.log(newState) // 印出 3
}, initialValue)

setState(3)
```

接下來就讓我們實際使用 useReducer 來完成 Counter!

### 使用 useReducer 改寫 Counter
先來看看之前的 useState 範例
```javascript
const Counter = () => {
  const [count, setCount] = React.useState(0)

  const increaseHandler = () => {
    setCount(count + 1)

  }

  const decreaseHandler = () => {
    if (count === 0) return

    setCount(count - 1)
  }

  return (
    <div className="container">
      <button className="minus" onClick={decreaseHandler}>-</button>
      <span className="number">{count}</span>
      <button className="plus" onClick={increaseHandler}>+</button>
    </div>
  )
};

ReactDOM.render(<Counter />, document.getElementById('root'))

```

將 useState 換成 useReducer ，並建立 countReducer
```javascript
function countReducer(state, newState){

}

const [count, setCount] = React.useReducer(countReducer, 0)
```
接下來只要在 countReducer 回傳新的 state ，就會更新 count 的數值並觸發 re-render。

```javascript
function countReducer(state, newState){
  return newState
}
```

計數器就順利的改寫完了，會發現說 useReducer 其實就跟 useState 差不多，只是多了一個 reducer callback 而已。

但實際是上，比較常見的做法會把 useReducer 回傳的 setState 稱作 dispatch ，而 reducer 的第二個參數為 action。

再來改寫一下 Counter 吧！

```javascript
function countReducer(state, action){
  switch (action.type) {
    case 'increment':
      return state + 1;
    case 'decrement':
      return state - 1;
    default:
      throw new Error(`no ${action.type} type in counterReducer`)
  }
}

const [count, dispatch] = React.useReducer(countReducer, 0)

const increaseHandler = () => {
  dispatch({type: 'increment'})

}

const decreaseHandler = () => {
  if (count === 0) return

  dispatch({type: 'decrement'})
}

```

順利的話，Counter 的功能就會如圖正常的運作
![](https://i.imgur.com/Io2it6D.gif)

以上就是關於 useReducer 的基本使用方式，但從上面的例子會發現，這樣寫起來比 useState 麻煩多了，為什麼要用 useReducer 呢？這個部分就讓我們明天來介紹吧！關於今天的內容有什麼問題都歡迎在下方留言～～
