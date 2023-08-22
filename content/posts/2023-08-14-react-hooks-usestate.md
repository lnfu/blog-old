---
title: "關於 React Hooks 的 useState()"
date: 2023-08-14T22:35:08+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

## 從 class 到 arrow function

之前在寫 React 時，我都使用 class extends React.Component 的寫法。

然而在有些時候，單純使用 arrow function 的寫法會更為簡潔。

不過如果一來要使用到 state 的功能就沒辦法直接在 class 裡面寫了。（已經沒有 class）

這時候我們可以引入 React Hooks 來使用 state。

## 使用範例

我們可以先練習從熟悉的 class 寫法來一步一步改成使用 arrow function + React Hooks 的寫法。

假設有以下 component：

```
class App extends React.Component {
    constructor(props) {
        super(props)
        this.state = {
            message: ""
        }
        this.greet = this.greet.bind(this)
    }

    greet() {
        this.setState({
            message: "Hello, World"
        })
    }

    render() {
        return <>
            <button onClick={this.greet}>Hello</button>
            <h1>{this.state.message}</h1>
        </>
    }
}
```

我們可以先把 class 改寫成 arrow function。

```
const App = (props) => {
    ;
}
```

接著把 `render()` 函數裡面的內容拉出來到 `App()`，然後刪除 `render()` 和 `constructor()`。

```
const App = (props) => {
    return <>
        <button onClick={this.greet}>Hello</button>
        <h1>{this.state.message}</h1>
    </>
}
```

然後我們使用 `useState()` 來新增一個 state。
> 記得要 import `useState()`。

> `useState()` 裡面的參數是 state 的預設值。

```
const [ message, setMessage ] = useState("")
```

然後改寫 `greet()` 函數。

```
const greet = () => {
    setMessage("Hello, World")
}
```

最後我們把原本 class 需要寫的 `this` 刪除，並且原本的 `this.state` 的遺跡改成直接使用 `message`。

```
const App = (props) => {
    const [ message, setMessage ] = useState("")

    const greet = () => {
        setMessage("Hello, World")
    }

    return <>
        <button onClick={greet}>Hello</button>
        <h1>{message}</h1>
    </>
}
```

這樣改完後是不是變得超級簡潔！如果想要再更精簡一點，我們也可以把 `greet()` 寫成在 `onClick` 後面。

```
onClick={() => {setMessage("Hello, World")}}
```

## 心得
想當初看別人寫 React 也是都用 `useState()` 的方式，但是學習時卻發現有點卡卡的，所以還是建議一開始學 React 的朋友可以先從最初的 class 寫法下手。
等到對整個架構稍微熟悉後再去改成使用 arrow function 和 React Hooks 的寫法就會非常簡潔。