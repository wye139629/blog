---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useState (2)'
date: '2021-09-26'
tags: ['React', 'JavaScript']
draft: false
summary: '在上一篇介紹了使用 hooks 的基本規則，以及 useState 的使用方式。今天來針對 initialValue 以及 setState 再做了解。'
images: []
---
在上一篇介紹了使用 hooks 的基本規則，以及 useState 的使用方式。今天來針對 initialValue 以及 setState 再做了解。

### Pass the function

寫過一陣子的 JavaScript 的話，應該都會知道，我們能夠將 function 當作參數傳遞進另一個 function 中。而 useState 的 initialValue 其實除了可以傳入基本型別以外，我們也能夠傳入一個 function ， 但必須回傳一個值作為初始值，如下

```javascript
const [state, setState] = useState(() => 'initialValue')
```

在 React 中這樣設定初始值的方式稱為 Lazy initialization 。

### 為什麼需要 Lazy initialization ？
先來看看昨天的 Counter 元件的練習

```javascript
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
    <div className="container">
      <button className="minus" onClick={decreaseHandler}>-</button>
      <span className="number">{count}</span>
      <button className="plus" onClick={increaseHandler}>+</button>
    </div>
  )
};

ReactDOM.render(<Counter />, document.getElementById('root'))
```
回想一下，當我們使用按下按鈕呼叫 setState 更新狀態的時候都會觸發 Counter 元件 re-render 。 換句話說，每次 state 更新的時候，都會重新呼叫一次 Counter function，所以 Counter 裡面的變數或是函式都會重新被定義及呼叫。

假設今天有個情況是我們的 initialValue 必須經過資料格式的轉換或計算等等。我們可能會這樣子寫：

```javascript
function transformData(rawData){
   //  將 rawData 進行複雜的換算...
   return initialValue
}
const App = (props) => {
  const initialValue = transformData(props)
  const [state, setState] = useState(initialValue)
}
```

但這樣就有個問題，同上面所提到的，每次 state 更新的時候，變數或是函式都會重新被定義及呼叫。這個 transformData 每一次都會再被呼叫，但是我們只希望function 中的運算只在第一次的時候執行並給我們 initialValue 而已。

這個時候我們就需要用到 Lazy initialization 了，我們就可以改寫成這樣

```javascript
function transformData(rawData){
  return () => {
   //  將 rawData 進行複雜的換算...
   return initialValue
  }
}
const App = (props) => {
  const [state, setState] = useState(transformData(props))
}
```

透過 Lazy initialization 比較複雜或是昂貴的計算就只會在第一次 render 的時候執行，之後當 state 改變的時候，就不會做多餘的計算了。

### setState
上面提到了 initialValue 可以是個 function ，其實 setState 我們也可以傳入一個 function 給它，寫起來如下

```javascript
setCount((previousCount) => { return previousCount + 1 })
// 傳入一個 function 會接收當下的狀態，回傳新的值做為新的狀態
```

那什麼時候需要這樣子寫，跟昨天介紹的直接傳入一個基本型別到差異是什麼呢？

我們將上面的 Counter 改寫，把 increaseHandler 中的 setCount 用 setTimeout 包起來如下

```javascript
const Counter = () => {
  const [count, setCount] = React.useState(0)

  const increaseHandler = () => {
    setTimeout(() => {
      setCount(count + 1)
    }, 1000)
    // 點擊 plus 按鈕，將 count 加 1 ，並使用 setCount 改變 Count 狀態
  }
```
這個時候我們在畫面連續按五下 + 的時候就會發現 count 只有加 1 次

![](https://i.imgur.com/eUcq7Xc.gif)

會造成這樣的原因主要是，這是一個 stale closure 的問題，有興趣可以看看[這篇](https://dmitripavlutin.com/react-hooks-stale-closures/)。總之在這一秒內，每一次在 setCount 的時候拿到的 count 都是 0，所以畫面最後也只會顯示 1 。

如果說我們今天期望顯示的結果是，我們按下幾次按鈕就會紀錄次數的話，我們就必須使用傳入 function 的解法，才能解決上面 stale closure 的問題。

```javascript
const Counter = () => {
  const [count, setCount] = React.useState(0)

  const increaseHandler = () => {
    setTimeout(() => {
      setCount(previousCount => previousCount + 1)
    }, 1000)
    // 點擊 plus 按鈕，將 count 加 1 ，並使用 setCount 改變 Count 狀態
  }
```

當我們改寫後再看看功能，我們能確保每一次執行 setCount ， previousCount 都會取得最新的值，並且做更新。功能也會如期顯示正確的次數了。

![](https://i.imgur.com/6yxSopI.gif)

所以當我們想要針對前一次的 state 做更新的時候都會建議使用傳入 function 的方式來確保我們不會遇到 stale closure 的問題。

以上就是今天關於 useState 的補充介紹，有問題歡迎在下方留言！明天我們會來瞭解 Derive state、 Lifting State 與 Colocate state 這三個概念。
