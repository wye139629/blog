---
title: 'Re: 新手讓網頁 act 起來 - Form'
date: '2021-09-22'
tags: ['React', 'JavaScript']
draft: false
summary: 'Form 一直都是網頁中重要的功能，今天就讓我們來瞭解在 React 處理 Form 該注意的事情吧！'
images: []
---
Form 一直都是網頁中重要的功能，今天就讓我們來瞭解在 React 處理 Form 該注意的事情吧！

### Controlled Component
在撰寫一個表單的時候我們常常都會使用到表單 Element，像是：input、textarea 和 select 。這三個與一般的 HTML Element 不太一樣，它們內部能夠保有自己的 state。 例如：

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <button type="submit">送出</button>
</form>
```

當我們在 input 輸入完後按下送出，form 會記住使用者輸入的值並與相對應的 label 一起送出。在 React 中會把有這樣子行為的 element 稱作 Uncontrolled Component。（ 其實就是把資料交給原生處理 ）

所以相反的什麼是 Controlled Component？ 就是這個 element 將 state 交給 React 來控管，我們就會稱它為 Controlled Component。

接下來我們就來練習一下，如何寫一個 controlled component 的表單吧！

首先，我們可以使用 React.useState()，來建立一個 state 。關於 useState 在後面的文章會再詳細介紹。

```javascript
const [name, setName] = React.useState('')
```
然後再 input 的 value 屬性掛上 name state
```html
<form>
  <label>
    Name:
    <input type="text" name="name" value={name} />
  </label>
  <button type="submit">送出</button>
</form>
```
這個時候打開 console 會發現一個警告的錯誤跳出來

![](https://i.imgur.com/5X929Hu.png)

會有這個錯誤訊息主要是因為我們將 state 交給 React 控管了，但是我們並沒有在這個 input 掛上 onChange，導致這個 input 是無法編輯的，所以如果我們要讓使用者能夠自由的輸入，我們還必須掛上 onChange 事件。

```javascript
const handleChange = (e) => {
  setName(e.target.value)
}

<form>
  <label>
    Name:
    <input type="text" name="name" value={name} onChange={handleChange} />
  </label>
  <button type="submit">送出</button>
</form>
```
最後，我們再將 onSubmit 事件掛在 form element 上，讓 React 來控管 form submit 的行為。完整的程式如下

```html
<!DOCTYPE html>
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
    function App() {

      const [name, setName] = React.useState('')

      const handleChange = (e) => {
        setName(e.target.value)
      }

      const handleSubmit = (e) => {
        e.preventDefault()
        alert('A name was submitted: ' + name)
      }

      return (
        <form onSubmit={handleSubmit}>
          <label>
            Name:
            <input type="text" name="name" value={name} onChange={handleChange} />
          </label>
          <button type="submit">送出</button>
        </form>
      )
    }

    ReactDOM.render(<App />, document.getElementById('root'))
  </script>

</body>

</html>

```
順利的話，當我們按下送出，就可以看見 alert 的視窗，並印出我們輸入的名子。

![](https://i.imgur.com/4YWiXOB.png)


最後來做個總結，當我們使用 Controlled component 等於是讓 React 來控管 form element ，使用者所輸入的內容都會透過事件來更新 React 的 state 來確保說我們 form element 的內容永遠會跟 state 是一樣且同步的，如此一來，我們能夠對使用者的輸入的內容進行操作，例如：設定初始值或是驗證等等。

以上就是今天的內容了，有什麼問題都歡迎在下方留言。
