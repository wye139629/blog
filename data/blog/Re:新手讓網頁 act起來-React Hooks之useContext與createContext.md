---
title: 'Re: 新手讓網頁 act 起來 - React Hooks 之 useContext 與 createContext'
date: '2021-10-05'
tags: ['React', 'JavaScript']
draft: false
summary: '在之前 Lift state 的文章有提過，當我們有兩個元件須共用到同一個 state 會將 state 提升然後再向下傳給需要的元件。那如果說不只兩個，可能有四、五個以上，那我們可能就會再提升，然後再透過 props 一層一層傳遞。除了透過 props 傳遞，其實我們還能用 context 來達到幫助我們將 state 傳遞下去，今天就讓我們來介紹如何使用 React context 吧！'
images: []
---
### 前言
在之前 Lift state 的文章有提過，當我們有兩個元件須共用到同一個 state 會將 state 提升然後再向下傳給需要的元件。那如果說不只兩個，可能有四、五個以上，那我們可能就會再提升，然後再透過 props 一層一層傳遞。除了透過 props 傳遞，其實我們還能用 context 來達到幫助我們將 state 傳遞下去，今天就讓我們來介紹如何使用 React context 吧！

### useContext 與 createContext

在使用 useContext 前必須先使用 createContext 來建立一個 Context object。它可以接收一個參數作為 defaultValue 。

```javascript
const Context = createContext(defaultValue)
```
而這個 Context 物件，會有 Provider 屬性，那它其實是 React element 所以我們可用 JSX 的形式呈現，並傳入 value props 。

```javascript
<Context.Provider value={/* some value */}>
  {children}
</Context.Provider>
```

而被 Context.Provider 所包含的元件，都可以取得 value。那究竟該怎麼取呢？ 這個時候就是用到 useContext 啦！

使用 useContext 並將我們一開始使用 createContext 所回傳的 Context object 傳入就能夠取得我們在 provider 提供的 value 了！

```javascript
const value = useContext(Context)
```

以上就是 useContext 與 createContext 的搭配使用，接下來就讓我們來看看範例，加深對 context 的印象吧！

### 範例

假設我們有個 App 結構如下

```javascript
// App.jsx
import { Header } from './components/Header'
import { Body } from './components/Body'
import { Footer } from './components/Footer'

function App() {
  const [ darkTheme, setDarkTheme ] = React.useState(false)
  const theme = darkTheme ? 'dark' : 'light'

  return (
    <>
      <Header themeState={[theme, setDarkTheme]}/>
      <Body theme={theme}/>
      <Footer themeState={[theme, setDarkTheme]}/>
    </>
  )
}

ReactDOM.render(<App />, document.getElementById('root'));
```
```javascript
// Header.jsx
import { ButtonTheme } from './ButtonTheme'

function Header({themeState}) {
  return (
    <div>
      <ButtonTheme themeState={themeState}/>
    </div>
  )
}
```
```javascript
// Footer.jsx
import { ButtonTheme } from './ButtonTheme'

export function Footer({themeState}) {
  return (
    <div>
      <ButtonTheme themeState={themeState}/>
    </div>
  )
}=
```

```javascript
// Body.jsx
export function Body({theme}) {
  return (
    <div>
      {theme}
    </div>
  )
}
```

```javascript
// ButtonTheme.jsx
export function ButtonTheme({themeState}) {
  return (
    <div>
      <Button themeState={themeState} />
    </div>
  )
}

function Button({themeState}) {
  const [theme, setDarkTheme] = themeState
  return (
    <button onClick={() => { setDarkTheme(prev => !prev) }}>
      {theme}
    </button>
  )
}
```

範例中，我們希望按下 Header 或是 footer 的按鈕都可以改變 theme 的值，這個時候第一種做法就是在 App 元件建立 useState 並傳下去讓 button 透過 onClick 去更改狀態，那 Body 元件也能夠同步更換 theme 。那我們就嘗試使用 context 來試試看!

1. 創建 ThemeContext 檔案，並使用 createContext 建立 ThemeContext，同時宣告

```javascript
// ThemeContext.js
const ThemeContext = React.createContext('light')
```

2. 建立 ThemeContext.Provider 的元件，並且將我們的 state 提供給 value

```javascript
export function ThemeContextProvider({ children }) {
  const [darkTheme, setDarkTheme] = React.useState(false)
  const theme = darkTheme ? 'dark' : 'light'

  return (
    <ThemeContext.Provider value={[theme, setDarkTheme]}>
      {children}
    </ThemeContext.Provider>
  )
}
```

3. 建立 useThemeContext
```javascript
export function useThemeContext() {
  const contextValue = React.useContext(ThemeContext)

  if (!contextValue) {
    throw new Error('you should wrap the component in theme context provider')
  }

  return contextValue
}
```

4. 接下來將 Header Body 與 Footer 元件包在 ThemeContextProvider 中

```javascript
// App.jsx
import { Header } from './components/Header'
import { Body } from './components/Body'
import { Footer } from './components/Footer'
import { ThemeContextProvider } from './components/ThemeContext'

function App() {
  return (
    <ThemeContextProvider>
      <Header />
      <Body />
      <Footer />
    </ThemeContextProvider>
  )
}

ReactDOM.render(<App />, document.getElementById('root'));
```

5. 將 Header 、Body 與 Footer 的用不到的 props 移除
```javascript
function Body() {

  return (
    <div>
      {theme}
    </div>
  )
}

function Header() {
  return (
    <div>
      <ButtonTheme />
    </div>
  )
}

function Footer() {
  return (
    <div>
      <ButtonTheme />
    </div>
  )
}

function ButtonTheme() {
  return (
    <div>
      <Button />
    </div>
  )
}
```

6. 在 Body 與 Button 中使用 useThemeContext()

```javascript
import { useThemeContext } from './components/ThemeContext'

function Body() {
  const [theme] = useThemeContext()

  return (
    <div>
      {theme}
    </div>
  )
}
```

```javascript
import { useThemeContext } from './components/ThemeContext'

function Button() {
  const [theme, setDarkTheme] = useThemeContext()

  return (
    <button onClick={() => { setDarkTheme(prev => !prev) }}>{theme}</button>
  )
}
```

![](https://i.imgur.com/49BnwzA.gif)


這樣我們就成功地使用 context 改寫啦！可以發先使用 context 的寫法，我們就不需要透過 props 一層一層的傳遞，只要在使用的元件中使用 useContext 就可以拿到 value。

以上就是關於 useContext 與 createContext 的使用方式！有什麼問題都歡迎在下方留言告訴我～～
