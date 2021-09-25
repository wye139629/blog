---
title: 'Re: 新手讓網頁 act 起來 - 再次瞭解React.createElement()'
date: '2021-09-18'
tags: ['React', 'JavaScript']
draft: false
summary: '在上一篇文章中我們簡單的介紹到如何使用 React.createElement()，並搭配 ReactDOM.render()，讓我們能夠順利的在畫面上印出 Hello world。今天就讓我們再多認是一點 React.createElement 這個 api 吧！'
images: []
---
## 前言
在上一篇文章中我們簡單的介紹到如何使用 React.createElement()，並搭配 ReactDOM.render()，讓我們能夠順利的在畫面上印出 Hello world。今天就讓我們再多認是一點 React.createElement 這個 api 吧！

## React.createElement()
昨天我們有提到說 createElement()，可以帶入三個以上的參數，然後會回傳 React Element 的物件。讓我們試著印出來觀察看看。
```javascript
const element = React.createElement(
'div',
{ className: 'container' },
'Hello world'
)

console.log(element)

ReactDOM.render(element, document.getElementById('root'))
```
![](https://i.imgur.com/ZjCYogT.png)

就讓我們來透過上面的 console 印出來的結果，來自行腦補並看圖說故事吧！

從結果來看，可以發現我們帶進去的三個參數，第一個參數會成為 type 屬性的值，第二個參數物件會成為 props 屬性的值，而最後一個 'Hello world'，也被收集成在 prop 物件 中的 children 屬性的值。

這個時候我們可以發揮科學家的精神，如果我們輸入四個或五個參數會怎樣？
```javascript
const element = React.createElement(
'div',
{className: 'container'},
'Hello world, ',
'React is fun ',
'and i love coding'
)
console.log(element)

ReactDOM.render(element, document.getElementById('root'))
```
讓我們來看看畫面與 console 的結果：
![](https://i.imgur.com/NYBnpoA.png)

除了畫面多印出我們新傳入的參數外，也可以發現我們多傳入的兩個參數也被收集成在 prop 物件裡 children 屬性，而且 children 變成陣列了，所以或許我們可以稍微改寫一下寫法，而且印出來的結果也會一樣。
```javascript
const element = React.createElement(
'div',
{
  children: [
  'Hello world, ',
  'React is fun ',
  'and i love coding'
  ],
  className: 'container'
}
)
console.log(element)

ReactDOM.render(element, document.getElementById('root'))
```
稍微來簡單統整一下到這邊為止我們對 React.createElement() 的瞭解：
 1. 會回傳 React element (就是一個物件而已)。
 2. 第一個參數能夠決定最後畫面渲染的 type。
 3. 第二參數需要是一個 object 會成為 element 的 props 值。
 4. 第三個參數之後的值都會被收集在 props 中的 children 陣列。

如果已經熟悉我們上面所知道的結果，那現在想要在畫面製造出巢狀的 HTML 結構就不是什麼難事了

```javascript
const greetingEl = React.createElement('span', {}, 'Hello world!, ')
const contentEl = React.createElement('span', {}, 'React is fun and i love coding')

const element = React.createElement(
'div',
{
  children: [
    greetingEl,
    contentEl
  ],
  className: 'container'
}
)
console.log(element)

ReactDOM.render(element, document.getElementById('root'))
```
畫面依然會順利的印出之前的東西，但是仔細看一下 Element 結構，就會發現已經是變成，div 包兩個 span 了：
![](https://i.imgur.com/mLlaiuc.png)

眼尖的你可能會發現，console 跑出了一個警告，說每個 child 應該要有唯一的 key prop。這個問題我們在之後的文章會再介紹。但如果說，你看到警告會很不爽，可以這麼做：
```javascript
const greetingEl = React.createElement('span', {}, 'Hello world!, ')
const contentEl = React.createElement('span', {}, 'React is fun and i love coding')

const element = React.createElement(
'div',
{ className: 'container' },
greetingEl,
contentEl
)
console.log(element)

ReactDOM.render(element, document.getElementById('root'))
```
將兩個 child 放回第三、四個參數的位置，這個警告就會消失了。

以上就是今天的內容，有什麼問題都歡迎在下方留言，明天我們就會來介紹常常在用的 JSX 啦。
