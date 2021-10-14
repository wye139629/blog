---
title: 'Re: 新手讓網頁 act 起來 - Error boundary'
date: '2021-10-13'
tags: ['React', 'JavaScript']
draft: false
summary: '在開發的過程中，難免會有些疏失而導致程式出現錯誤，最終畫面呈現一片空白。而在 React 的時候都會遇到沒有給 key 的錯誤訊息。究竟為什麼會有這個錯誤訊息呢？ 就讓我們來一起來了解 key 的基本概念吧！'
images: []
---
### 前言
在開發的過程中，難免會有些疏失而導致程式出現錯誤，最終畫面呈現一片空白。而在 React 中，我們可以利用 Error boundary (錯誤邊界) ，來幫助我們呈現錯誤例外的畫面，今天就讓我們來學習如何使用 Error boundary 吧！

### Error boundary

Error boundary 是一個 React Class Component，它能夠捕捉底下被包覆的子元件所拋出的錯誤，並且渲染事先定義好的預備畫面。透過這樣的機制，當發生錯誤被拋出來的時候，我們的 UI 不會因為錯誤而整個壞掉。接下來就讓我們來看看使用範例吧！

假設我們有一個文章區塊，當按下 See more 的時候，會發生錯誤，導致整個 App 停止運作，在正式的環境下會直接變成全白，而在開發環境 React 會跳出錯誤來。

```javascript
//App.js
import React from 'react'
import Post from './components/Post.js'
import './css/App.css'


function App() {
  return (
    <div className="App">
      <h1>My post:</h1>
      <Post />
    </div>
  );
}

export default App;
```

```javascript
//Post.js
import React, { useState } from 'react'
import '../css/Post.css'

const fetchData = () => new Promise((_, reject)=>{
  setTimeout(()=>{
    reject('faild to get data')
  }, 2000)
})

export function Post(){
  const [postData, setPostData] = useState({
    reqStatus: 'idle',
    content: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Iste facilis voluptas quis possimus reiciendis ducimus ab est quo nisi hic cumque officia dicta magni harum, ipsa accusantium eum culpa vel!...',
    error: null
  })
  const { reqStatus, content, error } = postData

  function clickHandler(){
    setPostData(prev => ({...prev, reqStatus: 'pending'}))
    fetchData()
      .then((res)=>{
        setPostData(prev => ({
          ...prev,
          reqStatus: 'successed',
          content: res.data
         }))
      })
      .catch(error => {
        setPostData(prev => ({
          ...prev,
          reqStatus: 'failed',
          error
         }))
      })
  }

  if(reqStatus === 'failed'){
    throw error
  }

  return(
    <div className='post-container'>
      <h2>How to use Error boundary ?</h2>
      <p>
        { reqStatus === 'pending' ?  'Loading....' : content }
      </p>
      <button onClick={clickHandler}>See more</button>
    </div>
  )
}

export default Post

```
#### production

![](https://i.imgur.com/OSNmfOD.gif)

#### development

![](https://i.imgur.com/bbhIkdA.gif)


這個時候我們就能使用 Error boundary 來幫助我們！

```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { error: null, errorInfo: null};
  }

  componentDidCatch(error, errorInfo) {
    this.setState({
      error,
      errorInfo
    })
  }

  render() {
    if (this.state.errorInfo) {

      return (
        <div>
          <h2>Something went wrong.</h2>
          <details style={{ whiteSpace: 'pre-wrap' }}>
            {this.state.error && this.state.error.toString()}
            <br />
            {this.state.errorInfo.componentStack}
          </details>
        </div>
      );
    }

    return this.props.children;
  }
}

```
在我們的 Error boundary 元件中，componentDidCatch 這個方法會接住底下任何子元件所拋出的錯誤以及拋出錯誤的地方。所以，我們在 render 方法中，可以透過 error 的 state 判斷要不要 render 底下的元件，或是我們的錯誤資訊。

![](https://i.imgur.com/KM0K8Et.gif)


### react-error-boundary

如果覺得自己寫一個 class component 很麻煩，我們也可以使用 [react-error-boundary](https://github.com/bvaughn/react-error-boundary) 這個套件，接下來讓我們就來使用它來改寫上面的範例吧！

1. 先安裝 react-error-boundary

```shell
yarn add react-error-boundary
```

2. 引入 ErrorBoundary

```javascript
import { ErrorBoundary } from 'react-error-boundary'
```

3. 定義 ErrorFallback 元件

```javascript
  function ErrorFallback({ error, resetErrorBoundary  }) {
    return (
      <div>
        <p>Something went wrong:</p>
        <pre>{error}</pre>
        <button onClick={resetErrorBoundary}>Try again</button>
      </div>
    );
  }
```

4.  在 ErrorBoundary 元件中的 FallbackComponent 屬性掛上 ErrorFallback
```javascript
function App() {
  return (
    <div className="App">
      <h1>My post:</h1>
      <ErrorBoundary
        FallbackComponent={ErrorFallback}
      >
        <Post />
      </ErrorBoundary>
    </div>
  );
}
```

![](https://i.imgur.com/dMnAzGy.gif)

這樣我們就順利的改寫成功了！關於 react-error-boundary 還有其他方便的方法可以使用，有興趣的朋友可以到它的 github 再做研究，以上就是今天的分享，如果有任何問題都歡迎在下方留言！
