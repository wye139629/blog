---
title: 'Re: 新手讓網頁 act 起來 - useCallback 與 React.memo'
date: '2021-10-08'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天介紹了 useCallback 的基本使用方式。今天來介紹如何使用 useCallback 與 React.memo 來減少 render 次數。'
images: []
---
昨天介紹了 useCallback 的基本使用方式。今天來介紹如何使用 useCallback 與 React.memo 來減少 render 次數。

首先，先來複習一下什麼情況下，會造成 re-render:

1. 元件的任一 props 或 state 更新
2. 父層元件更新

```javascript
function Button({ count, increament }) {
  console.log('%c Button component render', 'color: red')

  return <button onClick={increament}>{count}</button>
}

function Description() {
  console.log('%c Description render', 'color: green')

  return <div>click the button</div>
}

function App() {
  const [count, setCount] = React.useState(0)
  const increament = () => {
    setCount(count + 1)
  }

  console.log('%c App render', 'color: yellow')

  return (
    <div>
      <Button count={count} increament={increament} />
      <Description />
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'));
```
當我們 App 的 count state 改變時，會觸發 re-render ，底下的元件不管有沒有傳遞 props 或是 state 都會一起 re-render。

![](https://i.imgur.com/mkebrod.gif)


假設有個情況是我們需要 render list
```javascript
function Button({ count, increament }) {
  return <button onClick={increament}>{count}</button>
}

function MyList({ content, onClickHandler }) {
  console.log(`${content} MyList render`)

  return (
    <div onClick={onClickHandler}>
      {content}
    </div>)
}

function App() {
  const [count, setCount] = React.useState(0)
  const [list, setList] = React.useState(['1', '2', '3', '4', '5'])

  const increament = () => {
    setCount(count + 1)
  }

  const clickItem = (e) => {
    console.log(`click the ${e.target.textContent} item`)
  }

  console.log('%c App render', 'color: yellow')

  return (
    <div>
      <Button count={count} increament={increament} />
      {list.map(item => <MyList key={item} content={item} onClickHandler={clickItem} />)}
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'));
```
![](https://i.imgur.com/tdwTcL0.gif)


當我們點下按鈕改變 count 時，我們的所有的 list item 都會 re-render，可以 count 改變跟 list 一點關係都沒有。當我們這個 list 越來越多，render 也會增加，這樣的情況就可以考慮使用 React.memo 將 MyList 元件包起來。


### React.memo 與 useCallback
memo 這個是一個 HOC (High oreder component) ，接收一個元件並回傳一個元件，這過程中 memo 跟 useCallback 很類似，它會去記住傳進去的元件，並對照每一次 re-render 傳進來的值是不是有改變，去決定要不要 re-render 。

所以使用 React.memo 將 MyList 包起來後，就會去比對 content 跟 onClickHandler 是不是有變化，有變化就 re-render 。

```javascript
const MyList = React.memo(({ content, onClickHandler }) => {
  console.log(`${content} MyList render`)

  return (
    <div onClick={onClickHandler}>
      {content}
    </div>)
})
```
這個時候當我們再次按下按鈕改變 count state ，MyList 元件還是依然，re-render。為什麼呢？原因就是因為 onClickHandler 每一次都是新的實體。所以，這個時候就輪到 useCallback 登場了！

```javascript
const clickItem = React.useCallback((e) => {
  console.log(`click the ${e.target.textContent} item`)
}, [])
```

使用 useCallback 之後我們的 MyList 就不會因為 count 改變而 re-render 了！

![](https://i.imgur.com/KrmwVU5.gif)


以上是可以考慮使用 useCallback 的情境。

今天就先介紹到這邊，如果有任何問題都歡迎在下方留言！
