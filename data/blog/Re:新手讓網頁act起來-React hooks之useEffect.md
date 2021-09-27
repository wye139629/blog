---
title: 'Re: 新手讓網頁 act 起來 - React hooks 之 useEffect'
date: '2021-09-27'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天我們介紹完 React 最常用的 useState hook，今天就來聊聊在第二常用的 useEffect hook 吧！'
images: []
---
昨天我們介紹完 React 最常用的 useState hook，今天就來聊聊在第二常用的 useEffect hook 吧！

根據官網的說明，useEffect 在 function component 是扮演著處理 side effect 的角色。

看完上面簡短的說明，相信應該會有一個疑問是說，什麼是 side effect？首先要了解 side effect 我們先來簡單了解一下什麼是 pure function。

### Pure function
Pure function 指的是當相同的輸入，永遠會得到相同的輸出，而且沒有任何顯著的副作用。例如：

```javascript
function double(x){
  return 2 * x
}
double(2) // 4
double(2) // 4
double(2) // 4
```
在上面 double 函式中，我們很單純的只做運算，而且輸入相同的值 2，都只會輸出 4。

那到底怎麼樣算是一個副作用呢？例如：

```javascript
let doNotChange = 10

function double(x){
  console.log('hello') //  副作用

  doNotChange += 5 //  副作用
  return 2 * x
}


double(2) // 4
double(2) // 4
double(2) // 4
```
以上面的例子來看，我們呼叫 console.log() 更動瀏覽器的主控台的輸出，或是更動到外部的變數，像這樣有更動到外部環境都具有副作用。

副作用並不代表一定是不好的，而是它有可能會影響到其他環境的使用情況。

### useEffect
初步了解什麼是 pure function 及 side effect 之後，我們可以試想把 function component 作為一個 pure function 來看待。當我們傳入的 props 都是固定的，那回傳的 UI 就會是一樣的。而其他的，例如： 串接第三方 api 或是呼叫瀏覽器的 api 等等，都會被歸類為 side effect。

而 useEffect 這個 hook 就是設計來處理並執行這些 side effect 的。

以下是 useEffect 的使用方式。

```javascript
useEffect(()=>{
    // 每一次 render 後會執行
})

useEffect(()=>{
    // 只有在第一次 render 後執行
}, [])

useEffect(()=>{
    // 第一次 render 後會執行以外，每當 count 變數的值有變動時執行
}, [count])

useEffect(()=>{
    // 第一次 render 後執行以外，每當 count 變數的值有變動時執行，但會先等 return 的函式執行完後才會執行這裡
    return ()=>{
    // 第一次 render 不執行，第二次重新 render 後先執行這個函式
    }
}, [count])
```

useEffect 這個函式接收兩個參數，第一個參數是函式，要執行的 side effect 會放在這個函式中執行；第二參數則是一個陣列，被稱為 dependency array，因為 useEffect 會根據陣列中的值變化而決定第二次 render 後要不要執行第一個被當作參數傳進去的函式，如果是空陣列，那 useEffect 只會在第一次 render 過後執行。

讓我們在前幾天的 useState 的範例加上 useEffect，來觀察一下上面這四種使用方式：

```javascript
const Counter = () => {
  const [count, setCount] = React.useState(0)
  //使用 useState 建立 Count 狀態，並將 0 設為初始狀態


  React.useEffect(()=>{
    // 每一次 render 後會執行
    console.log('No.1')
  })

  React.useEffect(()=>{
    // 只有在第一次 render 後執行
        console.log('No.2')
  }, [])

  React.useEffect(()=>{
    // 第一次 render 後會執行以外，每當 count 變數的值有變動時執行
     console.log('No.3')
  }, [count])

  React.useEffect(()=>{
    // 第一次 render 後執行以外，每當 count 變數的值有變動時執行，但會先等 return 的函式執行完後才會執行這裡
    console.log('No.4')
    return ()=>{
    // 第一次 render 不執行，第二次重新 render 後先執行這個函式
      console.log('No.5')
    }
  }, [count])

  console.log('No.6 render component')  // render

  const increaseHandler = () => {
    setCount(prev => prev + 1)
    // 點擊 plus 按鈕，將 count 加 1 ，並使用 setCount 改變 Count 狀態
  }

  const decreaseHandler = () => {
    if (count === 0) return
    setCount(prev => prev - 1)
    // 點擊 minus 按鈕，將 count 減 1 ，並使用 setCount 改變 Count 狀態
  }

  return (
    <div>
      <button onClick={decreaseHandler}></button>
      <span>{count}</span>
      <button onClick={increaseHandler}></button>
    </div>
  )
};

ReactDOM.render(<Counter />, document.getElementById('root'));
```

大家可以猜猜看上面六個 console.log ，在第一次 render component  後，印出的順序會是什麼? 接著當 count 值改變，觸發 re-render 後，印出的順序又會是如何？

第一次 render 的解答：
```javascript
"No.6 render component"
"No.1" // 每一次 render 後會執行
"No.2" // 只有在第一次 render 後執行
"No.3" // 第一次 render 後會執行以外，每當 count 變數的值有變動時執行
"No.4" // 第一次 render 後執行以外，每當 count 變數的值有變動時執行，但會先等 return 的函式執行完後才會執行這裡
```

第二次 render 的解答：
```javascript
"No.6 render component"
"No.5" // 重新 render 後先執行這個函式
"No.1" // 每一次 render 後會執行
"No.3" // 第一次 render 後會執行以外，每當 count 變數的值有變動時執行
"No.4" // 第一次 render 後執行以外，每當 count 變數的值有變動時執行，但會先等 return 的函式執行完後才會執行這裡
```
不知道大家有沒有猜對呢？

以上就是 useEffect 基本的介紹，有什麼問題都歡迎在下方留言。
