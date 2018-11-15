# Chap 09. Vue Cli 的建置與運作原理

## 為什麼要用 VUE CLI

1. 基於 Webpack 所建置的開發工具
2. 便於使用各種第三方套件 (BS4, Vue Router)
3. 可運行 Sass, Babel 等編譯工具
4. 便於開發 SPA 的網頁工具
5. 靠簡單設定，就能搭建開發時常用的環境

### 使用 Vue CLI

- 幫你建立好檔案結構
- 提交 dist 資料夾即可
- 使用 npm 套件管理
- SPA：前端所模擬的路由
- 前後端分離

### VUE CLI 限制

- 不便於開發非 SPA 的網頁
  - 有此需求可用先前教的 CDN 模式

## 如何使用 Vue Cli

- node.js (>=6.x, 8.x preferred, npm version 3+ and Git)
- `npm i -g vue-cli`

vue 語法

- init 產生一個新專案
- list 是列出官方可用的樣板 (主要用 webpack 版本)

```shell
$ vue init webpack vuewebpack
```

```shell
$ cd vuewebpack
$ npm i
$ npm run dev
```

## Vue Cli 所產生的資料夾結構說明

![](https://imgur.com/HNbX3l7.png)

## Webpack 腳本介紹 及 自定義環境變數

- /build/webpack.*.conf.js
- /config/*.js
