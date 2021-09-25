---
title: 'Re: 新手讓網頁 act 起來 - PropTypes'
date: '2021-09-21'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天我們介紹完如何建立一個元件，今天就來介紹 PropTypes，讓建立的元件更加的完整吧！'
images: []
---
昨天我們介紹完如何建立一個元件，今天就來介紹 PropTypes，讓建立的元件更加的完整吧！

### PropTypes

當我們在寫元件的時候，會建議將 props 先定義好要接收什麼樣的型別，而這樣的好處是當別人要使用你寫好的元件時，會比較清楚該傳什麼樣的東西進去，降低產生 bug 的機率。

延續昨天的 Message 元件，它會接收兩個字串型別的 prop，但假設使用這個元件的人，忘記少傳一個 prop，或是傳入數字進來，如下:

```javascript
const Message = ({ color, animal }) => {
  return <div>{color} {animal}</div>
}

const element = (
  <div className='container'>
    <Message color={'Black'} />
    <Message color={3} animal={'dog'} />
  </div>
)

ReactDOM.render(element, document.getElementById('root'))
```
![](https://i.imgur.com/VrFVL6K.png)

畫面看起來都非常的正常，但這不是我們所希望看到的。至少我們期望，如果別人少傳或亂傳東西到這個元件，能夠在 console 印出錯誤訊息，來讓開發的人員注意到這個問題。

接下來就讓我們來使用 propTypes 來改善我們的 Message 元件。

首先在 Message 元件上建立 propTypes 的屬性物件，而這個物件的 key 就是你要定義型別驗證的 prop，以上面的範例來說，兩個 props 都要驗證。

```javascript
Message.propTypes = {
  color: PropTypes.string,
  animal: PropTypes.string
}
```

再來，我們的 value 要必須是一個 callback ，它會接收三個參數分別是 props、這個 prop 的名稱以及元件名稱。

```javascript
const PropTypes = {
  string(props, propName, componentName) {
    const type = props[propName]

    if (typeof type !== 'string') {
      throw new Error(
        `The ${componentName} component needs ${propName} prop to be type of 'string', but was passed a ${type} instead`
      )
    }
  }
}
```
把這兩段加進去後，再去看 console 就會出現我們所寫的錯誤訊息啦！

![](https://i.imgur.com/MP6bxSu.png)

### 使用 prop-types 套件

在上面的範例中我們自己寫了一個簡單的型別驗證，讓我們可以檢查傳進來的 props 是不是符合預期的型別。但其實我們可以使用這個 [prop-types](https://yarnpkg.com/package/prop-types) 套件來幫助我們做檢驗。

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
  <script src="https://unpkg.com/prop-types@15.7.2/prop-types.js"></script>

  <script type='text/babel'>

    const Message = ({ color, animal }) => {
      return <div>{color} {animal}</div>
    }

    Message.propTypes = {
      color: PropTypes.string
      animal: PropTypes.string
    }

    const element = (
      <div className='container'>
        <Message color={'Black'} />
        <Message color={3} animal={'dog'} />
      </div>
    )

    ReactDOM.render(element, document.getElementById('root'))
  </script>
</body>

</html>

```
引入 prop-types 之後，就可以把我們前面寫的 PropTypes 物件刪掉了，然後剛好我們之前的命名跟套件使用的物件一樣，所以會發現 console，依然會出現一個型別錯誤訊息，不過沒有給 animal prop 卻沒有錯誤提示。

![](https://i.imgur.com/2sCbVZq.png)


這個時候只要在 string 後面加上 isRequired，讓它知道這個 prop 是必填

```javascript
Message.propTypes = {
  color: PropTypes.string.isRequired,
  animal: PropTypes.string.isRequired
}
```
再看一下 console 就會出現兩個錯了

![](https://i.imgur.com/gokeScJ.png)

關於 prop-types 的套件使用，有興趣的人可以到它的 [Github](https://github.com/facebook/prop-types) 做深入的了解，
以上就是今天的內容了，有什麼問題都歡迎在下方留言。
