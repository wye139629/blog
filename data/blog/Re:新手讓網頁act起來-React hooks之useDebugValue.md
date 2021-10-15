---
title: 'Re: 新手讓網頁 act 起來 - React hooks 之 useDebugValue'
date: '2021-10-15'
tags: ['React', 'JavaScript']
draft: false
summary: '今天要介紹最後一個 React hook - useDebugValue ，它也是個較少使用的 hook ，且它的使用必須搭配 React dev tools 與 custom hook 。那究竟它是用來做什麼的呢？就讓我們來認識認識它吧！'
images: []
---
### 前言
今天要介紹最後一個 React hook - useDebugValue ，它也是個較少使用的 hook ，且它的使用必須搭配 React dev tools 與 custom hook 。那究竟它是用來做什麼的呢？就讓我們來認識認識它吧！

### useDebugValue
useDebugValue 的功用在當我們開啟 React dev tools 時，可以看到 custom hook 的標籤。讓我們直接拿昨天的範例來看看吧！

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

function App() {
  const [name, setName] = useLocalStorageState('name', '')

  function onChangeHandler(e) {
    setName(e.target.value)
  }

  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input id="name" type="text" value={name} onChange={onChangeHandler} />
      </form>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))
```
首先，讓我打開 React dev tools 來看看 App 元件，來觀察看看還沒使用 useDebugValue 的狀況。

![](https://i.imgur.com/WbYrVdU.png)

再來，我們在 useLocalStorageState 中加上，useDebugValue

```javascript
function useLocalStorageState(key, initialValue = '') {
  const [state, setState] = React.useState(() => {
    const storageValue = window.localStorage.getItem(key)
    if (storageValue) return JSON.parse(storageValue)

    return typeof initialValue === 'function' ? initialValue() : initialValue
  })

  React.useDebugValue('hello')

  React.useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(state))
  }, [state])

  return [state, setState]
}
```

![](https://i.imgur.com/Fnk8yXc.png)

可以發現 LocalStorageState 就多了 'hello' 的標籤。每一次當我們使用這個 custom hook 的時候就能夠給它不同的標籤，方便我們在檢查的時候快速的找到想找的 custom hook

```javascript
function useLocalStorageState(key, initialValue = '') {
  const [state, setState] = React.useState(() => {
    const storageValue = window.localStorage.getItem(key)
    if (storageValue) return JSON.parse(storageValue)

    return typeof initialValue === 'function' ? initialValue() : initialValue
  })

  React.useDebugValue(`${key}: ${state}`)

  React.useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(state))
  }, [state])

  return [state, setState]
}

function App() {
  const [name, setName] = useLocalStorageState('name', '')
  const [count, setCount] = useLocalStorageState('count', 0) // 多設置一個 state
//.....
}
```
![](https://i.imgur.com/ArwjLA4.png)


以上就是今天關於 useDebugValu 的基本認識，如果有任何問題都歡迎在下方留言！
