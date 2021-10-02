---
title: 'Re: 新手讓網頁 act 起來 - 探索 useEffect'
date: '2021-10-02'
tags: ['React', 'JavaScript']
draft: false
summary: 'key 是 React element 中的一個屬性，相信很多人在撰寫 React 的時候都會遇到沒有給 key 的錯誤訊息。究竟為什麼會有這個錯誤訊息呢？ 就讓我們來一起來了解 key 的基本概念吧！'
images: []
---
#
昨天介實作一個陽春的 useState ，今天來換 useEffect 吧！透過實作來幫助我們以後在寫這兩個 hooks 的時候，能夠更有畫面！

### myUseEffect

先來看看我們前兩天完成的 useState 的程式碼

```javascript
const hooks = [] // 使用陣列來收集每一次 hook 呼叫
let callId = 0 // 用來紀錄每一次 hook 呼叫的順序

function myUseState(initialValue) {
  const id = callId++
  if (hooks[id]) return hooks[id]

  function setState(newValue) {
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

const Counter = () => {
 //.....
}

function callRender() {
  callId = 0
  ReactDOM.render(<Counter />, document.getElementById('root'))
}
callRender()
```

我們在最外面設置了 hooks 陣列與 callId，來幫助我們紀錄每一個 hook 被呼叫的順序以及該紀錄的值。所以我們就可以利用這點，來紀錄 useEffect 中的 dependency array 。 為什麼要紀錄呢？就讓我們先來快速複習一下 useEffect 吧！

1. 如果沒有給二個參數，在每一次 render 後都會執行第一個 callback 參數
2. 會根據第二個陣列參數中的值，來決定是否執行第一個 callback 參數

所以要完成第二點，我們就需要利用 hooks array 來紀錄 dependency array，比對每一次 re-render 傳進來的 dependencies 與紀錄在 hooks 中的是不是都一樣。

接下來我們就來嘗試完成 myUseEffect 吧！

1. 當第一次呼叫時，執行 callback ，並將 dependency 存入 hooks 並增加 callId

```javascript
function myUseEffect(callback, depArray) {
  callback()
  hooks[callId] = depArray
  callId++
}
```

2. 之後每一次呼叫要取得之前紀錄的 dependency

```javascript
function myUseEffect(callback, depArray) {
  const prevDepArray = hooksp[callId]

  callback()
  hooks[callId] = depArray
  callId++
}
```

3. 比對先前紀錄的與當下的 dependency ，如果有變化就執行 callback

```javascript
function myUseEffect(callback, depArray) {
  const prevDepArray = hooksp[callId]
  let hasChange = true

  if (prevDepArray) {
    hasChange = depArray.some(
      (dep, idx) => !Object.is(dep, prevDepArray[idx])
    )
  }

  if (hasChange) callback()

  hooks[callId] = depArray
  callId++
}
```

4. 每一次 render 後才執行 callback

```javascript
function myUseEffect(callback, depArray) {
  const prevDepArray = hooksp[callId]
  let hasChange = true

  if (prevDepArray) {
    hasChange = depArray.some(
      (dep, idx) => !Object.is(dep, prevDepArray[idx])
    )
  }

  if (hasChange) {
    setTimeout(() => {
      callback()
    }, 100)
  }

  hooks[callId] = depArray
  callId++
}
```

這樣子，我們就土炮完成了 myUseEffect 了！

接下來，來試試看它是不是能夠運作，將 Counter 元件，加上 myUseEffect ，當 count 變化時，改變 document.title 。

```javascript
const Counter = () => {
  const [count, setCount] = myUseState(0)
  const [count2, setCount2] = myUseState(10)

  myUseEffect(() => {
    console.log('只執行第一次')
  }, [])

  myUseEffect(() => {
    document.title = `Click ${count} times`
  }, [count])

  myUseEffect(() => {
    console.log('只執行第一次，除非改變 count2')
  }, [count2])

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
};

```

順利的話，就會看到當我們按下 count ，document.title 會如預期改變，另外兩個 myUseEffect 都只會第一次執行。

![](https://i.imgur.com/eL19erR.gif)


以上就是今天的介紹，如果有任何問題都歡迎在下方留言，明天我們將在繼續介紹其他的 hooks!
