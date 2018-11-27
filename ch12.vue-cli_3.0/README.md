# Chap 13. Vue Cli 3.0

## 3.0 與 2.0 的差異說明及特色

- 提供完整 GUI 網頁介面
- 核心與插件方式提供轉換
  - TypeScript 支持
- 預設零配置，直接運行
- 一堆設定檔案採用 GUI 網頁方式
  - 不用一個一個查這些設定檔要怎麼做設定
  - 再也不用記指令
  - 提供更多設定資訊

## 安裝運行 Vue Cli 3.0

參考[官網](https://cli.vuejs.org/zh/guide/installation.html)

- 有安裝過 vue-cli 2.0 要先解除安裝
- Node.js 8.9+ 版本 (推薦 8.11.0)

```shell
$ npm uninstall -g vue-cli
$ npm install -g @vue/cli
```

```shell
$ vue --version
```

創建專案：

- cmd
- GUI

參考[官網](https://cli.vuejs.org/zh/guide/creating-a-project.html#vue-create)

```shell
$ vue create vue-3-record
```

設定檔可儲存起來方便之後產生專案使用

## 資料夾結構說明

### 結構：

- src/
  - assets/
  - components/
  - App.vue
  - main.js
- public/
  - favicon.ico
  - index.html

### 重點說明：

- 這版開始沒有一堆 webpack 設定檔，也不希望你去調整，都在 node_modules @vue 裡面
- src：都會編譯的資料夾；不想要編譯就放去 public 裡面
- import 的 .vue 在 cli 3.0 不能省略
- 要寫 scss 放在 src/assets 裡面

## 環境變數的設定

開發與正式環境的 api 路徑有所不同時，參考[官網](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E6%A8%A1%E5%BC%8F)

```
# .env
VUE_APP_SECRET=secret
```

要加前面 VUE_APP 前綴 src 裡面的檔案才可以使用這個變數。

有其他環境的需求：

1. 創建 `.env.{{stage}}`

  ```
  // .env.kk
  VUE_APP_API=http://localhost
  ```

2. package.json 的 script 加上 `--mode {{stage}}` 來執行

  ```json
  {
    //...
    "scripts": {
      "serve": "vue-cli-service serve --mode kk",
      "build": "vue-cli-service build",
      "lint": "vue-cli-service lint"
    },
    //...
  }
  ```

- .env.development 是預設的 `npm run serve` 環境變數名稱，可不需加入 `--mode development`
- .env.production 是預設的 `npm run build` 環境變數名稱，可不需加入 `--mode production`
- .env 權重最低

## 使用 GUI 介面創建專案

```shell
$ vue ui
```

## CLI 2.0 專案搬移到 3.0 實際演練
