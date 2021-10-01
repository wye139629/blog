---
title: 'Re: 新手讓網頁 act 起來 - 探索 useState (2)'
date: '2021-10-01'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天我們成功的完成一個超簡略版的 myUseState ，今天就讓我們再來把它寫完整一點吧！'
images: []
---
昨天我們成功的完成一個超簡略版的 myUseState ，今天就讓我們再來把它寫完整一點吧！

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
目前的 myUseState 雖然在計數器的範例中可以運作，但它還存在一個很大的問題。當我們想要再設置一個 count2 的 state 時，當呼叫第一個 setCount 時會去修改外面的 newStateValue 然後 re-render ，結果第二個 count2 會因為 newStateValue 有值了而拿到跟 count 一樣的值。

所以單一設置一個變數是不夠的，我們必須分別紀錄第一次呼叫與第二次呼叫的 myUseState，來區分說這個 state 是哪一個 myUseState 回傳的值。

為了紀錄每一次 myUseState 的呼叫，我們可以將 newStateValue 變數修改成陣列，並多設置一個 callId 來紀錄呼叫的順序。

```javascript
const hooks = [] // 使用陣列來收集每一次 hook 呼叫
let callId = 0 // 用來紀錄每一次 hook 呼叫的順序
```
所以，接下來當我們呼叫一次 myUseState 的時候，將 callId 作為 index 並將 state 與 setState 紀錄在 hooks 陣列中。

```javascript=
function myUseState(initialValue){
  const id = callId++
  if (hooks[id]) return hooks[id]

  function setState(newValue){
    if (hooks[id][0] === newValue) return

    hooks[id][0] = newValue
    callRender()
  }

  const stateArray = [initialValue, setState]
  hooks[id] = stateArray

  return stateArray
}
```
最後當我們觸發 re-render 的時候必須將 callId 重置，不然還是會一直拿到 initialValue 。

```javascript
function callRender(){
  callId = 0
  ReactDOM.render(<Counter />, document.getElementById('root'))
}
```

接下來我們就可以在我們的元件中新增一個 count2 的 state 來看看 myUseState 能不能正常運行。

```javascript
const Counter = () => {
  const [count, setCount] = myUseState(0)
  const [count2, setCount2] = myUseState(10)

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

      <button className="minus" onClick={() => { setCount2(count2 - 1) }}>-</button>
      <span className="number">{count2}</span>
      <button className="plus" onClick={() => { setCount2(count2 + 1) }}>+</button>
    </div>
  )
}

function callRender() {
  callId = 0
  ReactDOM.render(<Counter />, document.getElementById('root'))
}
callRender()
```

順利的話，畫面上兩個計數器都會正常的運作。

![](https://i.imgur.com/weCmkPC.gif)

最後 setState 要能夠接收一個 callback ，來幫我們更新 state ：

```javascript
function myUseState(initialValue){
  const id = callId++
  if (hooks[id]) return hooks[id]

  function setState(newValue){
    let nextValue
    typeof newValue === 'function' ? nextValue = newValue(hooks[id][0]) : nextValue = newValue
    if (hooks[id][0] === nextValue) return

    hooks[id][0] = nextValue
    callRender()
  }

  const stateArray = [initialValue, setState]
  hooks[id] = stateArray

  return stateArray
}
```

將傳進來的 value 先做判斷是不是 function ，是的話就將目前的 state 傳入，並準備接收新的值 ，然後更新 state ，這樣就可以將原本的 setCount 換掉：

```javascript
const Counter = () => {
  const [count, setCount] = myUseState(0)
  const [count2, setCount2] = myUseState(10)

  const increaseHandler = () => {
    setCount(prev => prev + 1)
  }

  const decreaseHandler = () => {
    if (count === 0) return
    setCount(prev => prev - 1)
  }

  return (
    <div className="container">
      <button className="minus" onClick={decreaseHandler}>-</button>
      <span className="number">{count}</span>
      <button className="plus" onClick={increaseHandler}>+</button>
    </div>
  )
}

function callRender() {
  callId = 0
  ReactDOM.render(<Counter />, document.getElementById('root'))
}
callRender()
```

![](https://i.imgur.com/4EoeXe1.gif)


以上我們就完成了一個簡單的 useState 了，明天就讓我們來試著完成 useEffect 吧！如果對於今天的內容有任何問題都歡迎在下方留言！
