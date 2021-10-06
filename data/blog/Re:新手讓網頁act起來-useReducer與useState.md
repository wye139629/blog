---
title: 'Re: 新手讓網頁 act 起來 - useReducer 與 useState'
date: '2021-10-06'
tags: ['React', 'JavaScript']
draft: false
summary: '前天我們介紹了 useReduecer 的基本使用方式，跟 useState 相比起來複雜許多，那究竟為什麼需要有 useReducer 呢？什麼樣的情況下適合使用 useReducer？就讓我們來瞭解它吧！'
images: []
---
前天我們介紹了 useReduecer 的基本使用方式，跟 useState 相比起來複雜許多，那究竟為什麼需要有 useReducer 呢？什麼樣的情況下適合使用 useReducer？就讓我們來瞭解它吧！

### Counter 範例

先來看看上次我們用 useReducer 改寫的 Counter

```javascript
function countReducer(state, action){
  switch (action.type) {
    case 'increment':
      return state + 1;
    case 'decrement':
      if (state === 0) return state
      return state - 1;
    default:
      throw new Error(`no ${action.type} type in counterReducer`)
  }
}

const Counter = () => {
  const [count, setCount] = React.useReducer(countReducer, 0)

  const increaseHandler = () => {
    dispatch({type: 'increment'})
  }

  const decreaseHandler = () => {
    dispatch({type: 'decrement'})
  }

//.....

```

再來看看 useState 版本：
```javascript
const Counter = () => {
  const [count, setCount] = React.useState(0)

  const increaseHandler = () => {
    setCount(count + 1)

  }

  const decreaseHandler = () => {
    if (count === 0) return

    setCount(count - 1)
  }
// .....

```

上次為了示範如何使用 useReducer來做計數器，但實際上如果去跟 useState 的情況做比較，用 useReducer 就多寫了很多東西，像是：reducer、 switch case 。所以如果是像計數器單一的 state 然後不會影響到其他的 state ，會建議用 useState 就好。

那反過來說，到底什麼狀況下一個 state 會影響另一個 state？

### 分頁與排序

假設今天有個需求是，點選頁碼會切換頁面的貼文，然後有個按鈕點下去會依照貼文的日期做降冪或升冪排序，同時切回第一頁。

```javascript
const App = () => {
  const [posts, setPosts] = useState([])
  const [page, setPage] = useState(1);
  const [sortBy, setSortBy] = useState({
    category: 'date',
    type: 'desc'
  });

  function sortHandler(e){
    const sortCategory = e.target.id

    setPage(1)
    setSortBy(prev => {
      const nextType = prev.type === 'desc' ? 'asc' : 'desc'

      return {
        ...prev,
        category: sortCategory,
        type: nextType
      }
    })
  }

  function pageHandler(e){
    setPage(e.target.id)
  }

  useEffect(() => {
    // fetch api and setPost

  }, [page, sortBy]);

  return // 一些 UI Layout
}
```

使用 useReducer
```javascript
function reducer(state, action){
  switch (action.type) {
    case 'change sort':
      const nextType = state.sortBy.type === 'desc' ? 'asc' : 'desc'
      const nextSortBy = { ...action.sortBy, type: nextType }
      return { ...state, page: 1, sortBy: nextSortBy }

    case 'change page':
      return { ...state, page: action.page  }

    default:
      throw new Error(`no ${action.type} type in reducer`)
  }
}

const App = () => {
  const [posts, setPosts] = useState([])
  const [payload, dispatch] = useReducer(reducer, {
    page: 1,
    sortBy: {
      category: 'date',
      type: 'desc'
    }
  })

  function sortHandler(e){
    const category = e.target.id

    dispatch({type: 'change sort', sortBy: { category } })
  }

  function pageHandler(e){
    dispatch({type: 'change page', page: e.target.id})
  }

  useEffect(() => {
    // fetch api and setPost

  }, [page, sortBy]);

  return // 一些 UI Layout
}
```

觀察一下這兩個範例的 sortHandler，用 useState 來實現的話，sortHandler 除了更改 sortBy 的 state 還要 reset page ，如果今天又增加一些需求，需要同時變更的 state 越來越多，或許這更 handler 就會越來越忙。

可以發現如果用 useReducer 來寫的話，會將判斷移到 reducer ，也不需要再呼叫 setPage，然後 sortHandler 會變的很單純，就算因為需求的增加而變複雜，在 reducer 中能夠做的擴充性也會比單純使用 useState 來的好，handler 可能也不會因此增加太多事情。

透過 dispatch 的方式，能夠相對清楚定義好這些動作該做哪些事情，能讓我們增加擴充性外，也讓 handler 中的程式碼更好閱讀。

最後補充，其實 useState 是用 useReducer 做出來的！

以上就是今天的介紹，關於今天的內容有什麼問題都歡迎在下方留言～～
