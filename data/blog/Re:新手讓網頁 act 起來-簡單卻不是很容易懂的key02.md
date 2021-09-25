---
title: 'Re: 新手讓網頁 act 起來 - 簡單卻不是很容易懂的 key（2）'
date: '2021-09-24'
tags: ['React', 'JavaScript']
draft: false
summary: '昨天我們介紹了 key 的基本使用方式，今天我們就一起來瞭解為什麼需要 key 吧！'
images: []
---
昨天我們介紹了 key 的基本使用方式，今天我們就一起來瞭解為什麼需要 key 吧！

### 為什麼需要 key
首先，在暸解為什麼之需要 key 之前，要先知道 React 在每一次的 state 更新都會觸發 re-render ， render 完之後會比較當次與前一次 Virtual DOM 的差異（Diff 演算），最後才會去更新 DOM 。

知道每一次 re-render 都會都會比較差異後，我們就來沙盤推演一下，在沒有設置 key 的情況，React 會去怎麼做。

假設一開始我們有一個五個 li
```html
<ul>
  <li>red</li>
  <li>blue</li>
  <li>yellow</li>
  <li>green</li>
  <li>purple</li>
</ul>
```
1. 第一次更新，刪掉 purple，新的結構會長這樣
```html
<ul>
  <li>red</li>
  <li>blue</li>
  <li>yellow</li>
  <li>green</li>
</ul>
```
我們從頭開始比對，比較跟前一次的差異，發現最後一個 purple 不見了，
所以我們找出前後兩者個差異就是
```
-      <li>purple</li>
```
2. 第二次跟更新，刪掉 red ，新的結構會長這樣
```html
<ul>
  <li>blue</li>
  <li>yellow</li>
  <li>green</li>
</ul>
```
然後一樣跟前一次比對，這個時候就會發現
```html
前一次                          新的
<ul>                           <ul>
  <li>red</li>      ->           <li>blue</li>
  <li>blue</li>     ->           <li>yellow</li>
  <li>yellow</li>   ->           <li>green</li>
  <li>green</li>    ->
</ul>                          </ul>
```
結果就會變成：
```
-       <li>red</li>
-       <li>blue</li>
-       <li>yellow</li>
-       <li>green</li>

+       <li>blue</li>
+       <li>yellow</li>
+       <li>green</li>
```
可以仔細觀察一下旁邊 Elements 的變化
![](https://i.imgur.com/eiwGDhW.gif)

從我們上面的範例可以得知，React 其實沒辦法準確的知道使用者是刪除或修改哪一個 item，但它還是必須去執行，所以這個時候，就會把 index 作為 key 來執行，這也就是為什麼不建議使用陣列的 index 作為 key 的關係。

除了效能之外，當元素有 uncontrolled component 時會造成非預期的渲染。

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
    function App() {
      const [colors, setColors] = React.useState([
        'red',
        'blue',
        'yellow',
        'green',
        'purple'
      ])

      const removeItem = (targetItem) => {
        setColors((prev) => prev.filter(item => item != targetItem))
      }

      return (
        <ul>
          {colors.map((item, idx) => {
            return (
              <li>
                <button onClick={() => { removeItem(item) }}>x</button>
                <input type="text" defaultValue={item} />
                {item}
              </li>
            )
          })}
        </ul>
      )
    }

    ReactDOM.render(<App />, document.getElementById('root'))
  </script>
</body>

```

![](https://i.imgur.com/iqdCuSi.gif)


可以發現 input 的 value 並不是交給 React 來管理，所以當我們刪除第一個 li 時，其實是將 red 修改成 blue ， input 的 defalutValue 也確實有改，但 value 的 state 本身是交給 Html form element 管理，input 並沒有整個換掉，所以 value 依然保持 red。

所以當我們在 render list 的時候要特別注意這個 list 會不會做修改，或是有沒有用到 uncontrolled component ，如果有那就不能使用 index 作為 key。

當然如果不想思考這麼多，其實只要確保在 render list 都給唯一的 key 就不會有什麼問題了。

以上就是今天的介紹，有什麼問題都歡迎在下方留言。



參考資料：
https://epicreact.dev/why-react-needs-a-key-prop/
