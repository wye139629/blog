---
title: 'Re: 新手讓網頁 act 起來 - 簡單卻不是很容易懂的 key （1）'
date: '2021-09-23'
tags: ['React', 'JavaScript']
draft: false
summary: 'key 是 React element 中的一個屬性，相信很多人在撰寫 React 的時候都會遇到沒有給 key 的錯誤訊息。究竟為什麼會有這個錯誤訊息呢？ 就讓我們來一起來了解 key 的基本概念吧！'
images: []
---
key 是 React element 中的一個屬性，相信很多人在撰寫 React 的時候都會遇到下面的錯誤訊息

![](https://i.imgur.com/uaDCKdV.png)

究竟為什麼會有這個錯誤訊息呢？ 就讓我們來一起來了解 key 的基本概念吧！

### Render list
如果還有印象，在前面介紹 React.createElement 的時候，當我們的 children 是一個陣列的時候就會顯示，要求要有 key 的錯誤訊息。
所以我們可以這樣子做：

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
    const colors = [
      'red',
      'yellow',
      'orange',
      'blue'
    ]

    const element = (
      <ul>
        {colors.map(color => {
          return (
            <li>
              {color}
            </li>
          )
        })}
      </ul>
    )

    ReactDOM.render(element, document.getElementById('root'))
  </script>
</body>

</html>
```
這個時候畫面就會順利出現 list， console 也會如預期的出現需要 key 的錯誤訊息

![](https://i.imgur.com/PmYNb7H.png)

那要怎麼樣才能擺脫這個錯誤訊息呢？其實非常簡單，我們只要在每個 list 加上 key 的屬性，值可以是字串或是數字但必須是唯一的。

這個時候相信不少人會想到用陣列的 index 來當作 key，以上面的例子來看不會有太大的問題，因為我們只是要單純 render list，但在會做陣列新增修改的情況下，就不推薦用 index 作為 key，因為這樣效能上會比較不好。

這邊假設每個 color 的值不會有重複的情況下，可以作為 key
```javascript
const element = (
  <ul>
    {colors.map(color => {
      return (
        <li key={color}>
          {color}
        </li>
      )
    })}
  </ul>
)
```
加上去之後，console 的錯誤訊息就消失啦。

最後我們來補充一下，除了必須是唯一的以外，使用 key 要注意的地方。
1. key 只需要是 list 中唯一的值，不需要是整個 App

```javascript
const element = (
  <ul>
    {colors.map(color => {
      return (
        <li key={color}>
          {color}
        </li>
      )
    })}

    {colors.map(color => {
      return (
        <li key={color}>
          {color}
        </li>
      )
    })}
  </ul>
)
```
只要在兩個獨立的陣列中是唯一的值，就可以合法的 render 出畫面。

![](https://i.imgur.com/cioTwpQ.png)

2. key 必須加在 list element 中的最外層

```javascript
const element = (
  <ul>
    {colors.map(color => {
      return (
        <li>
          <span key={color}>{color}</span>
        </li>
      )
    })}
  </ul>
)
```
這樣寫就會發現 console 又會跑出錯誤訊息，應改要把 key 掛在最外層的 li 上。

3. key 不會出現在 props 中

```javascript
function Items(props) {
  console.log(props.key) // undefined

  return <li>{props.value}</li>
}

const colors = [
  'red',
  'yellow',
  'orange',
  'blue'
]

const element = (
  <ul>
    {colors.map(color => {
      return (
        <Items key={color} value={color} />
      )
    })}
  </ul>
)
```
![](https://i.imgur.com/h1mu9Sb.png)

以上是關於 key 的基本介紹，不過究竟為什麼要有 key 呢？ 這個問題就讓我們明天來好好瞭解吧！有什麼問題都歡迎在下方留言。
