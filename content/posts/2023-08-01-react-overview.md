---
title: "React"
date: 2023-08-01T14:44:45+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

React 是 Facebook 開發和維護的 Javascript library。

## JSX

React 使用 **JSX** 語法，可以看成是 Javascript 的擴展，讓我們能夠在 Javascript 中寫 HTML 並保有像是程式語言（Javascript）的可程式化性質。

JSX 檔案最終會由 **Babel** transcompiler 成真正的 Javscript 程式碼。

`class` 在 JSX 中由於是 Javascript 關鍵字所以變成 `className`。


## Component

component 概念可以說是 React 的核心。

> 個人觀點是這樣做使得 HTML 物件能夠輕鬆的被封裝、結構化、reuse...

作為 componenet 的 class/function 名稱開頭必須大寫。

### 寫法一（原生 Javascript 函數）
```
const MyComponent = function() {
  return <div>Hello, World</div>
}
```

### 寫法二（使用 ES6 class 語法）

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return <div>Hello, World</div>
  }
};
```

## ReactDOM

ReactDOM 是用來 render JSX 物件或是 component 到 HTML DOM 的 API。

```
ReactDOM.render(componentToRender, targetNode)
```

例如：
```
ReactDOM.render(<App />, document.getElementById('root'))
```


## Props
要如何在 component 之間傳遞參數呢（parent -> child）？答案是 Props（properties）。

範例：
```
<App>
  <CurrentDate date={Date()}/>
</App>

const CurrentDate = (props) => <p>The current date is: {props.date}</p>
```

> Date() 是 Javascript 的原生函數，會回傳當前時間。

另外，component 也可以設定 default props。

```
MyComponenet.defaultProps = {name: "Enfu Liao"}
```

### PropTypes

https://legacy.reactjs.org/docs/typechecking-with-proptypes.html#proptypes




```
class MyCompo extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return <div>this.props.name</div>;
  }
}

MyCompo.defaultProps = {
  name: "Enfu Liao"
}

MyCompo.propTypes = {
  name: PropTypes.string.isRequired
}
```


## state（狀態）

React 的 virtual DOM 會看 state 資料是否有更新，如果有就會重新渲染部份頁面（actual DOM）。

```
constructor(props) {
  this.state = {
    name: "Enfu Liao"
  }
}
```

```
<div>this.state.name</div>
```

### `setState()`

如果要更新 state 則必須使用 `setState()`：
```
this.setState({
  name: "Enya Lial"
})
```

> 要能夠使用到 `this.state`，class 的 method 比須加上 `this.methodName = this.methodName.bind(this);`和 class 綁定（詳細問題我目前不太清楚）

> 另外可能也需要複習一下 Javascript `this` 的使用時機（雖然也可以自己亂試解決）

有時候我們更新 state 會需要依賴原本 state 或是 props 的值，那麼就要採用以下寫法（因為更新是非同步（async），不能直接使用 `this.state` 或是 `this.props`）：

```
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

```
this.setState(state => ({
  counter: state.counter + 1
}));
```


