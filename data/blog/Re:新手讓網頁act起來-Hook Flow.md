---
title: 'Re: 新手讓網頁 act 起來 - Hook Flow'
date: '2021-09-28'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天在介紹 useEffect 的時候有提到 useEffect 中的執行順序，今天就再做進一步的觀察，來加深對於 useEffect 在 Component mount、 update 與 unmount 三個狀況下的執行時機吧！'
images: []
---
### 前言
昨天在介紹 useEffect 的時候有提到 useEffect 中的執行順序，今天就再做進一步的觀察，來加深對於 useEffect 在 Component mount、 update 與 unmount 三個狀況下的執行時機吧！

### 範例

建立 App 與 Child 元件，並在 App 元件中設立 showChild 的 state 來控制要不要顯示 Child 元件。並且在兩個元件中都設置三個 useEffect 分別是沒有 dependency array、空陣列與放置一個 state 的 dependency array。

```javascript
const Child = () => {
  console.log('%c    Child: render start', 'color: MediumSpringGreen')
  const [count, setCount] = useState(()=> {
    console.log('%c    Child: useState(() => 0)', 'color: tomato')
    return 0
  })

  useEffect(() => {
    console.log('%c    Child: useEffect(() => {})', 'color: LightCoral')
    return () => {
      console.log(
        '%c    Child: useEffect(() => {}) cleanup',
        'color: LightCoral',
      )
    }
  })

  useEffect(() => {
    console.log(
      '%c    Child: useEffect(() => {}, [])',
      'color: MediumTurquoise',
    )
    return () => {
      console.log(
        '%c    Child: useEffect(() => {}, []) cleanup',
        'color: MediumTurquoise',
      )
    }
  }, [])

  useEffect(() => {
    console.log('%c    Child: useEffect(() => {}, [count])', 'color: HotPink')
    return () => {
      console.log(
        '%c    Child: useEffect(() => {}, [count]) cleanup',
        'color: HotPink',
      )
    }
  }, [count])

  const increaseHandler = () => {
    setCount(prev => prev + 1)
  }

  console.log('%c    Child: render end', 'color: MediumSpringGreen')
  return (
    <button className="plus" onClick={increaseHandler}>{count}</button>
  )
};

function App() {
  console.log('%cApp: render start', 'color: red')

  const [showChild, setShowChild] = useState(()=>{
    console.log('%cLazy initialize', 'color: MediumSpringGreen')

    return false
  })

  function onChangeHandler(){
    setShowChild(prev => !prev)
  }

  useEffect(() => {
    console.log('%cApp: useEffect(() => {})', 'color: LightCoral')
    return () => {
      console.log('%cApp: useEffect(() => {}) cleanup', 'color: LightCoral')
    }
  })

  useEffect(() => {
    console.log('%cApp: useEffect(() => {}, [])', 'color: MediumTurquoise')
    return () => {
      console.log(
        '%cApp: useEffect(() => {}, []) cleanup',
        'color: MediumTurquoise',
      )
    }
  }, [])

  useEffect(() => {
    console.log('%cApp: useEffect(() => {}, [showChild])', 'color: HotPink')
    return () => {
      console.log(
        '%cApp: useEffect(() => {}, [showChild]) cleanup',
        'color: HotPink',
      )
    }
  }, [showChild])

  console.log('%cApp: render end', 'color: red')
  return (
    <div className="App">
      <input type='checkbox' value={showChild} onChange={onChangeHandler} />
      <div>
        { showChild ? <Child /> : null}
      </div>
    </div>
  );
}
```
畫面與功能會如下

![](https://i.imgur.com/jBuR11n.gif)

接下來我們來觀察一下console.log 會在瀏覽器的 console 印出什麼吧！

1. 剛載入 App 時 hooks 中的

![](https://i.imgur.com/JBUHDEv.png)

從印出的結果可以發現 useState 中的 Lazy initialize 會在元件開始呼叫之後馬上執行，而 useEffect 會在 render 之後開始依照順序執行。

2. 當我們按下 input 之後，讓 Child 元件顯示

![](https://i.imgur.com/dp5q7wO.png)

按下 input 會更新 App 中的 showChild state ，並 re-render，那 Lazy initialize 就不會再觸發了。之後開始呼叫 Child 元件，一樣會先執行 useState 的 Lazy initialize 。

接下來值得注意的是，因為 App 元件是 update 的 re-render 所以會觸發之前註冊的 useEffect  cleanup function。然後執行完之後會先從子層的 useEffect 開始執行，再執行父層的 useEffect。

3. 按下 Child 的 button 更新 count

![](https://i.imgur.com/lVZBsQC.png)

更新 Child 元件的 count state 只會影響 Child 元件，並不會影響到 父層，所以 Child 觸發 re-render 然後先執行 useEffect cleanup function 再執行 useEffect 。

4. 再次按下 input unmount Child 元件

![](https://i.imgur.com/Z7aHRfi.png)

再次按下 input 之後，State 更新觸發 App re-render ，然後因為 Child 元件 unmount 所以會觸發 Child 的 useEffect cleanup function，然後再執行 App 的 useEffect cleanup function ，接者執行 useEffect 。

最後我們可以針對 Mount、Update 與 Unmount 做個小小的整理:

### Mount
1. 執行 Lazy initialize
2. React render
3. React 更新 DOM
4. 瀏覽器渲染畫面
5. 執行 useEffect

### Update
1. React render
2. React 更新 DOM
3. 瀏覽器渲染畫面
4. 執行 useEffect 的 cleanup function
5. 執行 useEffect

### Unmount
 1. 執行 useEffect 的 cleanup function

最後的最後關於比較完整的 hooks flow 的圖表可以看 Donavon 的 Github [hook-flow ](https://github.com/donavon/hook-flow)
