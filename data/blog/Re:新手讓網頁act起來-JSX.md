---
title: 'Re: 新手讓網頁 act 起來 - JSX'
date: '2021-09-19'
tags: ['React', 'JavaScript']
draft: false
summary: '前面兩篇基礎的介紹 React.createElemnt()，但實際再開發上很少真的直接寫它，想想看光是寫一巢狀結構，就要些一堆 code 而且又不太好讀，所以大部分的情況都會是寫 JSX ， 那究竟什麼是 JSX 呢？ 就讓我來一步步的了解它吧！'
images: []
---
### 前言

前面兩篇基礎的介紹 React.createElemnt()，但實際再開發上很少真的直接寫它，想想看光是寫一巢狀結構，就要些一堆 code 而且又不太好讀，所以大部分的情況都會是寫 JSX ， 那究竟什麼是 JSX 呢？ 就讓我來一步步的了解它吧！

### JSX

先直接來看看 JSX 寫來起來的樣子吧！
```html
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id='root'></div>


  <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>

  <script>
    cosnt element = <div className='container'> Cool! This is JSX syntax </div>

    ReactDOM.render(element, document.getElementById('root'))
  </script>
</body>
</html>
```
仔細看你會發現，除了 class 是寫成 className 以外，根本就是在寫 HTML 標籤。
接下來呢，我們馬上開啟瀏覽器來看一下，就會發現畫面上沒有印出我們想要的結果，而且 console 那邊還有噴錯誤。

![](https://i.imgur.com/KhzWJl9.png)

這是怎麼一回事呢？ 稍微退一步想一下，等等在 JS 中可以這樣寫 HTML 語法嗎？ 瀏覽器根本看不懂，我們必須先用 Babel 來翻譯它，讓它是一個 React element，那要怎麼產生 React element？ 就是用 React.createElement() 啦，所以 Babel 會幫我將 JSX 翻譯成 React.createElemnt() 的語法來產生 React element。這也是為什麼會稱 JSX 是 React.createElemnt() 的語法糖衣。

接下來，就讓我們來引用 Babel 吧！並在 script 加上 type 屬性：

```html
<html lang="en">

<head>
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

    const element = <div className='container'> Cool! This is JSX syntax </div>


    ReactDOM.render(element, document.getElementById('root'))
  </script>
</body>

</html>
```
順利的話，畫面就會印出 Cool! This is JSX syntax！

![](https://i.imgur.com/lZ7PAJK.png)

然後到開發者工具的 Elements，點 Head 中的 script，可以看到 Babel 翻譯過後的樣子

![](https://i.imgur.com/6ZHNi2o.png)


接下來讓我們來看看 JSX 還能夠怎麼做一些變化。不過先讓我們來看一下 ES6 的樣板字面值（Template literals）， 我們可以在 ${} 中嵌入 expression。
```javascript
const greeting = 'Hello!'
const language = 'JavaScript'
const sentence = `${greeting} ${language}`
```
那 JSX 也有這樣的功能，不過是一個大括號 {}，所以我們可以這樣寫：
```javascript
const className = 'container'
const content = 'Cool! This is JSX syntax'

const element = <div className={className}>{content}</div>

```
要注意! 不要在 JSX 寫 statement，像是如下：

```javascript
const className = 'container'
const language = 'JavaScript'
const content = 'Cool! This is JSX syntax'

const element = <div className={className}>{ if(language === 'JavaScript'){ return content } }</div>
// 這樣寫就如同下面

// React.createElement('div', {className: 'container'}, if(language === 'JavaScript'){ return content } }</div>
)
```
你不會把一個 if statement 當作參數塞到一個 function call 中。
最後，既然知道 JSX 是 React.createElemnt 的語法糖，寫法上我們還可以搭配 spread operator 做一點點變化

```javascript
const props = {
  className: 'container',
  children: 'Cool! This is JSX syntax'
}

const element = <div {...props}/>
```

以上就是今天的 JSX 介紹，有什麼問題都歡迎在下方留言，明天就讓我們來介紹怎麼定義一個元件吧！
