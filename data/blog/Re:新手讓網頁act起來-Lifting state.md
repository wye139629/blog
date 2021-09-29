---
title: 'Re: 新手讓網頁 act 起來 - Lifting state'
date: '2021-09-29'
tags: ['React', 'JavaScript']
draft: false
summary: '剛學習 React 的時候，一定會碰到一個小問題，就是當我們有個值需要存在在兩個元件之間，該怎麼處理？ 而這個問題的答案很簡單就是將 State 提升，今天就讓我們來學習這個概念吧！'
images: []
---
### 前言

剛學習 React 的時候，一定會碰到一個小問題，就是當我們有個值需要存在在兩個元件之間，該怎麼處理？ 而這個問題的答案很簡單就是將 State 提升，今天就讓我們來學習這個概念吧！

### 提升 State

假設今天有個需求是要做一個 BMI 計算功能。畫面需要有兩個 input 分別接收身高與體重與一個按鈕，按下按鈕會跑出 alert 視窗並顯示 BMI。

```javascript
import { useState } from 'react'

function calculateBMI({ height, weight }){
  if (!Number(height)) return NaN

  const heightNum = Number(height) / 100
  const weightNum = Number(weight)
  const result = weightNum / (heightNum * heightNum)

  return result.toFixed(2)
}

function BMIform(){
  const [ bodyInfo, setBodyInfo ] = useState({
    height: '',
    weight: ''
  })

  const { height, weight } = bodyInfo

  const onChangeHandler = (e) => {
    const { name, value } = e.target
    setBodyInfo(prev => ({...prev, [name]: value }))
  }

  const onSubmitHandler = (e) => {
    e.preventDefault()
    const result = calculateBMI(bodyInfo)
    const text = isNaN(result) ? '請輸入正確的身高與體重' : `您的 BMI 為：${result}`

    alert(text)
  }

  return(
    <form onSubmit={onSubmitHandler}>
      <label>
        身高(公分):
        <input name='height' value={height} onChange={onChangeHandler}/>
      </label>
      <label>
        體重(公斤):
        <input name='weight' value={weight} onChange={onChangeHandler}/>
      </label>
      <button>計算</button>
    </form>
  )
}


function App() {
  return (
    <div className="App">
      <BMIform />
      <Display type='BMI' value={}/>
    </div>
  )
}

export default App
```

順利的話當我們輸入正確的身高與體重，按下計算，會成功跳出 alert 來。

![](https://i.imgur.com/nXzJrl0.gif)


接下來假設拿到了需求變更，用 alert 覺得不太好，想要改成在下方顯示 BMI。然後我們已經有一個 Display 的元件，但需要傳入 type 與 value 才能顯示。

```javascript
function Display({ type, value }){
  return(
    <div>
      您的 {type} 為： {value}
    </div>
  )
}

function App() {
  return (
    <div className="App">
      <BMIform />
      <Display type="BMI" />
    </div>
  )
}
```

這個時候我們必須把 state 從 BMIform 元件中提升到 App 元件，也就是共用 state 的兩個元件最近的父層元件。將 state 提升後，在分別將 state 作為兩個元件 props 傳遞下去。所以我們可以將範例改成這樣:

```javascript
function BMIform({ bodyInfo, onBMIChange }){
  const { height, weight } = bodyInfo

  return(
    <form>
      <label>
        身高(公分):
        <input name='height' value={height} onChange={onBMIChange}/>
      </label>
      <label>
        體重(公斤):
        <input name='weight' value={weight} onChange={onBMIChange}/>
      </label>
    </form>
  )
}

function Display({ type = 'BMI', value = null }){
  return(
    <div>
      您的 { type } 為： {value}
    </div>
  )
}

function App() {
  const [ bodyInfo, setBodyInfo ] = useState({
    height: '',
    weight: ''
  })

  const result = calculateBMI(bodyInfo)

  const onBMIChange = (e) => {
    const { name, value } = e.target

    setBodyInfo(prev => ({ ...prev, [name]: value }))
  }

  return (
    <div className="App">
      <BMIform bodyInfo={bodyInfo} onBMIChange={onBMIChange}/>
      { isNaN(result) ? '請輸入正確的身高與體重' : <Display type='BMI' value={result}/> }
    </div>
  )
}

export default App
```
![](https://i.imgur.com/XHHWt9Y.gif)


透過將共享的 state 提升到最靠近它們的共同父層，能夠確保兩個元件的資料來源都是通一份，並且保持同步。以上就是今天關於提升 state 的練習與介紹。有問題都歡迎在下方留言。
