---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useState (1)'
date: '2021-09-25'
tags: ['React', 'JavaScript']
draft: false
summary: 'React hooks 是在 React 16.8 版本才加進來的功能。那到底什麼是 Hooks 呢？它其實就是 React 提供的內建函式，能讓我們在 Function component 中使用 State 及生命週期的功能。
'
images: []
---
### 前言
React hooks 是在 React 16.8 版本才加進來的功能。那到底什麼是 Hooks 呢？它其實就是 React 提供的內建函式，能讓我們在 Function component 中使用 State 及生命週期的功能。

當我們使用 Hooks 時，有一些規則須遵守，如下：
*  Class component 無法使用，只能在 Function component 使用。
```javascript
// Class component
class Foo extends React.Component {
  render() {
    return <h1>Hello</h1>
  }
}

// Function component
function Boo() {
  return <h1>Hello</h1>
}
```

* 只在元件或是自定義的 Hook 最上層呼叫 Hook，不要在條件式、迴圈、巢狀 function 呼叫。這樣子能夠確保每一次 component 渲染的時候，這些 Hooks 被呼叫的順序都是一致的。
```javascript
function Foo() {
  const [state, setState] = useState() //正確的

    // 以下錯誤示範
  if(true){
    const [state, setState] = useState()
  }

  for(let i = 0; i < 5; i++){
    const [state, setState] = useState()
  }

  function boo(){
    const [state, setState] = useState()
  }
}
```
### useState()
useState 它是用來管理狀態的函式，基本的使用方式如下：

```javascript
const [state, setState] ＝ useState(initialValue)
```

當 useState 函式被呼叫時，會回傳含有兩個值的陣列，第一個值是狀態，第二個值是修改狀態的函式（命名慣例上會是 set + 前面 state 的名稱）。

所以想要變更 state 的值時，就使用 setState 這個函式，一旦 state 的值改變了，就會觸發 re-render， 接著 state 就會更新。

例如：

```javascript
const [count, setCount] = useState(0) // 建立一個初始值為 0 的計數狀態

setCount(count + 1) //利用 setCount 更改 count 的值
```

現在就讓我們運用上面的基礎概念，實作個簡單的計數器，來練習使用 useState 吧！

```html
<html lang="en">
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>

<body>
  <div id='root'></div>
  <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>

  <script type='text/babel'>
    const Counter = () => {
      const [count, setCount] = React.useState(0)
      //使用 useState 建立 Count 狀態，並將 0 設為初始狀態

      const increaseHandler = () => {
        setCount(count + 1)
        // 點擊 plus 按鈕，將 count 加 1 ，並使用 setCount 改變 Count 狀態
      }

      const decreaseHandler = () => {
        if (count === 0) return

        setCount(count - 1)
        // 點擊 minus 按鈕，將 count 減 1 ，並使用 setCount 改變 Count 狀態
      }

      return (
        <div>
          <button onClick={decreaseHandler}>-</button>
          <span>{count}</span>
          <button onClick={increaseHandler}>+</button>
        </div>
      )
    };

    ReactDOM.render(<Counter />, document.getElementById('root'))

  </script>
</body>

</html>
```
![](https://i.imgur.com/xjdt3Zk.gif)

以上就是今天基本 useState 的介紹，有問題歡迎在下方留言！明天我們會再繼續聊聊 useState 其他的小細節。
