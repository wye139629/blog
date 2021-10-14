---
title: 'Re: 新手讓網頁 act 起來 - React hooks 之 Custom hook'
date: '2021-10-14'
tags: ['React', 'JavaScript']
draft: false
summary: 'Custom hook 讓我們可以將 component 的邏輯提取出來到一個 function 中，讓我們可以重複的使用它，甚至運用在不同的 component 中。而當我們要寫一個 Custom hook 時，要注意這個 function 的命名一定要是 use 開頭，因為這樣子 React 才會知道你在這個 function 中使用了 hook ，並檢查是否有違反 Hook 規則的行為。接下來就讓我們來實際練習看看，如何寫出一個 Custom hook 吧！'
images: []
---
### 前言
Custom hook 讓我們可以將 component 的邏輯提取出來到一個 function 中，讓我們可以重複的使用它，甚至運用在不同的 component 中。而當我們要寫一個 Custom hook 時，要注意這個 function 的命名一定要是 use 開頭，因為這樣子 React 才會知道你在這個 function 中使用了 hook ，並檢查是否有違反 Hook 規則的行為。接下來就讓我們來實際練習看看，如何寫出一個 Custom hook 吧！


### useLocalStorageState
```javascript
function App() {
  const [name, setName] = React.useState(() => {
    return window.localStorage.getItem('name') || ''
  })

  function onChangeHandler(e) {
    setName(e.target.value)
  }

  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  }, [name])

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input id="name" type="text" value={name} onChange={onChangeHandler} />
      </form>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'));
```
上面的範例中，我們有個 input，當值改變的時候我們會存入 localStorage ，而瀏覽器重新整理時，會先去看 localStorage 是否有值，並設為初始值。範例的程式碼看起來沒有太大的問題，但假設，我們有其他的 state，也需要存入 localStorage，或許就可以考慮把這個行為做抽象化。接下來讓我們來試著完成 useLocalStorageState 吧！

1. 定義 useLocalStorageState
```javascript
function useLocalStorageState(){

}
```

2. 期望當使用 useLocalStorageState 一樣會回傳 state 與 setState
```javascript
function useLocalStorageState(initialValue = ''){
  const [state, setState] = React.useState(initialValue)

  return [state, setState]
}

const [name, setName] = useLocalStorageState(initialValue)
```
3. 將 useEffect 與 lazyInitalize 放進 useLocalStorageState

```javascript
function useLocalStorageState(initialValue = ''){
  const [state, setState] = React.useState(()=>{
     return window.localStorage.getItem('name') || initialValue
  })

   React.useEffect(() => {
    window.localStorage.setItem('name', name)
  }, [name])

  return [state, setState]
}
```

4. 將 key 設為引數，並更改 dependency
```javascript
function useLocalStorageState(key, initialValue = ''){
  const [state, setState] = React.useState(()=>{
     return window.localStorage.getItem(key) || initialValue
  })

   React.useEffect(() => {
    window.localStorage.setItem(key, state)
  }, [state])

  return [state, setState]
 }

```

5. 考量傳進來的 initialValue 是 callback，以及將存入的資料轉為字串，取的時候轉回原本格式

```javascript
function useLocalStorageState(key, initialValue = '') {
  const [state, setState] = React.useState(() => {
    const storageValue = window.localStorage.getItem(key)
    if (storageValue) return JSON.parse(storageValue)

    return typeof initialValue === 'function' ? initialValue() : initialValue
  })

  React.useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(state))
  }, [state])

  return [state, setState]
}

```
這樣子我們就完成一個基本的 useLocalStorageState Custom hook 了，接下來我們就可以很容易的增加想要存進 localStoragae 的 state 了！
```javascript
function App() {
  const [name, setName] = useLocalStorageState('name', '')
  const [count, setCount] = useLocalStorageState('count', 0)

  function onChangeHandler(e) {
    setName(e.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input id="name" type="text" value={name} onChange={onChangeHandler} />
      </form>
      <button onClick={() => setCount(count + 1)}>{count}</button>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'));
```

![](https://i.imgur.com/NP8cf0Z.gif)

以上就是今天的分享，如果有任何問題都歡迎在下方留言！
