---
title: "Tailwind CSS 基本使用"
date: 2023-08-16T13:12:15+08:00
draft: false
author: "Enfu Liao"
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

stack：Vite + React


```
yarn add tailwindcss postcss autoprefixer --dev
npx tailwindcss init -p
```


修改 `tailwind.config.js`（content 的值）。

```
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

修改 `src/index.css`（Vite 預設的內容都直接刪掉就好）。

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```
