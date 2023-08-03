---
title: "React"
date: 2023-08-01T14:44:45+08:00
draft: true
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

## PropTypes

https://legacy.reactjs.org/docs/typechecking-with-proptypes.html#proptypes