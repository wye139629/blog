---
title: 'Re: 新手讓網頁 act 起來 - 建立元件'
date: '2021-09-20'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天我們提到了該如何撰寫 JSX，今天就來學習怎麼建立元件，來把同類型的 React element 做抽象化吧～'
images: []
---
昨天我們提到了該如何撰寫 JSX，今天就來學習怎麼建立元件，來把同類型的 React element 做抽象化吧～

### Function Component
通常在寫 code 的時候，如果遇到重複性很高的程式碼，往往會用函式把它包起來，等有需要這程式碼的時候再呼叫函式使用它，來避免一直寫重複的 code。那在 React 中，也是同樣的道理，我們可以將重複性很高 UI 結構，抽象化成一個元件，當要使用的時候丟資料進去就好。以下用簡單的例子示範：

```html
<html lang="en">
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>

<body>
  <div id='root'></div>
  <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone@7.9.3/babel.js"></script>

  <script type='text/babel'>
    const message = (props) => {
      return <div>{props.children}</div>
    }

    const element = (
      <div className='container'>
        {message({children: 'Black cat'})}
        {message({children: 'Orange dog'})}
      </div>
    )

    ReactDOM.render(element, document.getElementById('root'))
  </script>
</body>

</html>


```
![](https://i.imgur.com/xxeyyXn.png)

順利的話，就能在畫面上看到兩行 message。不過其實真正在寫的時候不會這樣做。不知道大家有沒有印象，在最一開始介紹 React.createElement 的時候第一個參數接收 type，其實這個 type 可以接收一個會回傳一個 React element 的函式。讓我們將上面的程式碼改寫一下。

```javascript
const message = (props) => {
  return <div>{props.children}</div>
}

const element = (
  <div className='container'>
    {React.createElement(message, {}, 'Black cat')}
    {message({ children: 'Orange dog' })}
  </div>
)
```
這個時候大家可能會好奇，結果不是都一樣，為什麼還要這樣改寫？看起來結果的確都一樣，但是如果 chorme 有安裝 react devtool ，打開它之後就會發現兩者呈現的結構會是不一樣的。

![](https://i.imgur.com/kLO2ou9.png)


接下來再讓我們更近一步，既然我們能夠寫成 React.createElemnt 的形式，那我們是不是也能夠寫成 JSX 呢？ 現來讓我們複習一下，一般產生 div 會是怎麼樣子：

```javascript
const jsEl = React.createElement('div', {}, 'hello world')

const jsxEl = <div>hello world</div>
```
仔細觀察一下規則，可以猜測第一個參數 'div'，最終就會是 JSX tag 的名稱，所以我們可以再改寫成這樣：

```javascript
const message = (props) => {
  return <div>{props.children}</div>
}

const element = (
  <div className='container'>
    <message>black cat</message>
    {React.createElement(message, {}, 'Orange dog')}
  </div>
)
```
這個時候看一下畫面，會發現好像成功了，但 console 卻有錯誤跑出來，並貼心的告訴我們瀏覽器看不懂 ```<message>``` ，可以將 m 換成大寫。這是什麼原因呢？這個時候再去打開 head 中的 script 就會發現 Babel 轉換成的結果會是
```
React.createElemnet('message', null, 'black cat')
```
但其實我們希望的結果是像第二個那樣子，是一個 fuction expression，所以如果我們想要將元件寫成 JSX 形式，第一個字母要大寫，那 Babel 才會知道 ，「 喔！原來是傳入一個 function 進來啊！」。 所以我們只要把 function 名稱改成大寫就不會噴錯了。

```javascript
const Message = ({ children }) => {
  return <div>{children}</div>
}

const element = (
  <div className='container'>
    <Message>black cat</Message>
    <Message>orange dog</Message>
  </div>
)
```

### React Fragments
先來看一下上面範例的結構：
```html
<div id='root'>
  <div className='container'>
    <div>black cat</div>
    <div>orange dog</div>
  </div>
</div>
```
如果說，我們不想要 container 的話，ㄧ般來說就是直接把那個 div 刪除。但當我們嘗試這樣子做，會造成語法上的錯。

```javascript
const Message = ({ children }) => {
  return <div>{children}</div>
}

const element = (
    <Message>black cat</Message>
    <Message>orange dog</Message>
)

```
因為我們不會想要同時塞兩個值到同個變數中。這個時候我們就可以使用 React.Fragment 來幫我們處理這個問題。可以把它想像成一個隱形的容器幫我們把 React element 裝起來。

```javascript
const Message = ({ children }) => {
  return <div>{children}</div>
}

const element = (
  <React.Fragement>
    <Message>black cat</Message>
    <Message>orange dog</Message>
  </React.Fragement>
)
```

最後，如果想要偷懶寫少一點字，其實可以改成這樣就好，結果會是一樣的。

```javascript
const element = (
  <>
    <Message>black cat</Message>
    <Message>orange dog</Message>
  </>
)
```

以上就是今天的介紹，有什麼問題都歡迎在下方留言。
