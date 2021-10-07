---
title: 'Re: 新手讓網頁 act 起來 - Reack Hooks 之 useCallback'
date: '2021-10-07'
tags: ['React', 'JavaScript']
draft: false
summary: 'key 是 React element 中的一個屬性，相信很多人在撰寫 React 的時候都會遇到沒有給 key 的錯誤訊息。究竟為什麼會有這個錯誤訊息呢？ 就讓我們來一起來了解 key 的基本概念吧！'
images: []
---
### 前言
useCallback 常常會被用來優化效能，減少 React 不必要的 render ，但如果沒有好好理解它，濫用 useCallback 的話反而會導致效能更差。今天就讓我們來了解一下 useCallback 的基本用法，跟使用時機吧！

### useCallback
如果之前對 useEffect 夠熟悉的話，useCallback 應該會蠻快上手的。它跟 useEffect 一樣接受兩個參數：

1. callback
2. dependency array

然後回傳一個 memoized callback ，而這個 callback 就是第一個參數傳進去的 callback

```javascript
const memorizeCallback = useCallback(() => {
  // do something
}, [])
```
那什麼是 memoized callback 呢? 你可以想像 useCallback 將你傳進去的 function 紀錄起來，並且再吐還給你，當下次元件 re-render 的時候，會根據你的 dependency array 來決定要不要生成一個新的 function ，還是將上次紀錄下來的吐出來。

所以正常來說，如果沒有使用 useCallback 的話，在元件中定義的 function ，當每次 re-render 都會生成一個新的。

如果還沒有畫面，可以看看下面的範例：

```javascript
  function App() {
    const [count, setCount] = React.useState(0)
    const callbackRef = React.useRef()

    const test = () => {
      console.log('test callback')
    }

    React.useEffect(() => {
      callbackRef.current = test
    }, [])

    React.useEffect(() => {
      console.log(test === callbackRef.current)
    }, [count])

    return (
      <div>
        <button onClick={() => setCount(count + 1)}>{count}</button>
      </div>
    )
  }

  ReactDOM.render(<App />, document.getElementById('root'));)
```
打開 console 就會發現第一次會是 true ，而當 count 變化 re-render 後，test 的 function 就跟一開始的不一樣了，所以都得到 false。

使用 useCallback ：
```javascript
  const test = React.useCallback(() => {
    console.log('test callback')
  }, [])
```

如此一來，當之後 re-render test 都會是最一開始的的實體。

那什麼時候需要 useCallback 來幫助我們紀錄 function 呢？接下來就讓我們來看看範例吧！

### 範例

當我們在使用打 api 的時候往往建議將 function 定義在 useEffect 當中：

```javascript
React.useEffect(()=> {
  function fetchData(query){
    const baseUrl = `xxxxxxxx?${query}`
  }

fetchData('JavaScript')

}, [])
```

但假設我們其他方也需要用到這個 fetchData 時，我們就必須將它定義在外面

```javascript
function fetchData(query){
  const baseUrl = `xxxxxxxx?${query}`
}

React.useEffect(()=> {
  fetchData('JavaScript')
}, [])

React.useEffect(()=> {
  fetchData('React')
}, [])
```

這個時候如果按照 eslint 的提醒，我們就必須把 fetchData 放到 dependency array 中，但這樣做如果別的 state 改變了，會導致 re-render 然後 fetchData 又會是新的實體，就會造成 useEffect 執行。所以這個時候，其中一種的解決方案就是使用 useCallback 。

```javascript
const fetchData = React.useCallback((query) => {
  const baseUrl = `xxxxxxxx?${query}`
}, [])

React.useEffect(()=> {
  fetchData('JavaScript')
}, [fetchData])

React.useEffect(()=> {
  fetchData('React')
}, [fetchData])
```

這樣子 fetchData 就能夠很安全的放到 useEffect 的 dependency array 中了！

再來如果說 query 是從 state 來的話，我們就需在 useCallback 的 dependency array 放上 query

```javascript
const [query, setQuery] = React.useState('JavaScript')

const fetchData = React.useCallback((query) => {
  const baseUrl = `xxxxxxxx?${query}`
}, [query])

React.useEffect(()=> {
  fetchData()
}, [fetchData])
```
以上就是第一種 useCallback 的使用情境，當我們需要將 function 放入 useEffect 的 dependency array 時，要先思考一下，這樣子是不是想要達到的效果，還是需要用到 useCallback 將它紀錄起來。明天我們將再繼續聊聊，什麼樣的情況下適合用 useCallback 來減少 render 次數，以及什麼時候是不必要用的情況！

今天的介紹就到這邊，有什麼問題都歡迎在下方留言告訴我！
