---
title: 'Re: 新手讓網頁 act 起來 - useMemo 和 useCallback'
date: '2021-10-10'
tags: ['React', 'JavaScript']
draft: false
summary: '在前面幾天介紹了使用了 useCallback 或 useMemo 來做效能優化，不知道會不會有人跟我一樣，在剛瞭解完這兩個 hooks，就想說之後我在 function component 中定義的 callback 都要用 useCallback 包起來讓它只會生成一個實體，或是用 useMemo 來記住某些值。今天就讓我們來聊聊，為什麼沒事不要用 useCallback 或 useMemo 吧！'
images: []
---
### 前言
在前面幾天介紹了使用了 useCallback 或 useMemo 來做效能優化，不知道會不會有人跟我一樣，在剛瞭解完這兩個 hooks，就想說之後我在 function component 中定義的 callback 都要用 useCallback 包起來讓它只會生成一個實體，或是用 useMemo 來記住某些值。今天就讓我們來聊聊，為什麼沒事不要用 useCallback 或 useMemo 吧！

### 範例
```javascript
function App() {
  const [click, setClick] = React.useState(0)
  const clickHandler = () => { setClick(prev => prev + 1) }

  return (
    <div>
      <button onClick={clickHandler }>{click}</button>
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))
```

上面有個簡易計數器，clickHandler 做的事情很單純就是將 click 的值加上一。這個時候當我們使用 useCallback 包起來，可能會想說之後每一次 count 改變我們的 clickHandler 的都會是跟第一次宣告的一樣。不會再增加了。看起來好像效能比較好～～

```javascript
const clickHandler = React.useCallback(() => {
  setClick(prev => prev + 1)
}, [])
```

但事實上並非如此，我們來把傳進去的 callback 拆到外面去看看

```javascript
const clickHandler = () => { setClick(prev => prev + 1) }

const memorizeClickHandler = React.useCallback(clickHandler)
```

仔細觀察會發現，其實當我們每一次 re-render 都會產生一個新的 clickHandler 然後丟到 useCallback 作為參數，並且呼叫 useCallback ，這個時候會根據 dependency array ，去比對要會傳新的 clickHandler 還是舊的。以上面的範例來說，跟沒有使用 useCallback 的情況比起來，程式碼的複雜度提高了，要處理的東西更多了，反而更不好。

那 useMemo 呢？ useMemo 也是同樣的概念，例如：

```javascript
function double(number){
  return 2 * number
}

function App() {
  const [number, setNumber] = useState(1)
  const value = useMemo(() => double(number), [number])
  // ...
}

```
double 這個函式不是一跟特別昂貴的計算，但使用 useMemo 特別將它記錄起來，就會有種小題大作的感覺。事實上也是，因為就像上面提到的，當我們使用 useCallback 或是 useMemo 程式碼的複雜度會提高，要處理的東西更多。

所以，當我們在使用他們做效能優化的時候，要想到他們事實上是有代價的，而要處理的效能問題是否與值得付出這個代價。

以上就是今天的分享，如果有任何問題都歡迎在下方留言！

參考文章
https://kentcdodds.com/blog/usememo-and-usecallback
