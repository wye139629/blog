---
title: 'Re: 新手讓網頁 act 起來 - 探索 useState (1)'
date: '2021-09-30'
tags: ['React', 'JavaScript']
draft: false
summary: '經過前面幾篇基礎介紹，應該對 useState 與 useEffect 有一定程度的認識，俗話說：「打鐵要趁熱」，在繼續介紹其他的 Hooks 之前，這兩天我們就透過之前所學來刻一個 useState ，藉由這個機會來更加了解這個 hook。'
images: []
---
# ### 前言
經過前面幾篇基礎介紹，應該對 useState 與 useEffect 有一定程度的認識，俗話說：「打鐵要趁熱」，在繼續介紹其他的 Hooks 之前，這兩天我們就透過之前所學來刻一個 useState ，藉由這個機會來更加了解這個 hook。

### myUseState
下面是之前介紹 useState 的計數器範例。但我們做了一個更改就是將 React.useState() 換成 MyUseState()，所以目前這段程式碼是完全壞掉的。
```javascript
const Counter = () => {
  const [count, setCount] = myUseState(0)

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
接下來就來一步步實作 myUseState 吧！

我們先來想想看對於 useState 最基本的了解：
1. 接收一個任意基本型別做為 initialValue 並回傳一個陣列
2. 回傳的陣列有兩個值，分別是 state 與一個設定 state 的函式
3. 當我們使用 setState 的時候，我們會更新 state 並觸法 re-render。

依照上面所知的線索我們可以先完成基本的雛形：

```javascript
function myUseState(initialValue){
    let state = initialValue

    function setState(newValue){
    // update state
    // call re-render
    }

  return [state, setState]
}
```
re-render 的部分我們可以先嘗試重新讓 ReactDOM.render 重新呼叫一次來達成：

```javascript
function myUseState(initialValue){
  let state = initialValue

  function setState(newValue){
    // update state
    callRender()
  }

  return [state, setState]
}


function callRender(){
  ReactDOM.render(<Counter />, document.getElementById('root'))
}

callRender()
```
再來就是如何更新 state 並且再每一次的呼叫會丟出更新的值。如果說更新的 state 與現在一樣就不會 re-render。

依照目前的雛形，當我們傳入新的值到 setState 無法直接去更改 myUseState 中的 state 變數。就算改變了，後面觸發 re-render ，myUseState 會重新被被呼叫 iniialValue 傳進來的還是一開始的預設值，最後回傳的還是預設值。

所以我們必須在 myUseState 外面的 scope 將值先存起來

```javascript
let newStateValue

function myUseState(initialValue){
  if (newStateValue) return [newStateValue, setState]
  let state = initialValue

  function setState(newValue){
    if (newStateValue === newValue) return

    newStateValue = newValue
    callRender()
  }

  return [state, setState]
}

```
來看一下畫面，計數功能就恢復正常了，一個非常非常基本的 useState 就完成了～～

![](https://i.imgur.com/URO3at0.gif)


雖然目前看起來功能是正常的，但事實上還缺很多東西，例如： setState 是要能夠傳入 callback 並更新 state 或是我們能夠在一元件定義呼叫多個 myUseState 等等。

明天我們將繼續完成它！如果對於今天的內容有問題都歡迎在下方留言～～
