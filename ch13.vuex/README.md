# Chap 13. Vuex

## Vuex 是什麼？

- 管理整個前端網站資料所使用的
- 網站規模夠大才使用
- $eventbus
  - 適合同層級元件
  - 簡單的情境
- 全域變數
  - 無法雙向綁定
- 把所有資料跟方法移到 Vuex 管理，所有元件都可使用，並維持雙向綁定

### 元件方法對應

- data -> state
- methods -> action
- computed -> getter
- mutation --> 改變資料內容的方法 (vuex 特有)

![](https://imgur.com/jnaeQBv.png)

- state：資料狀態
- action：如同 methods，進行非同步的行為及取得資料
  - 不會改變資料狀態
- getter：如同 computed，資料呈現的方式
  - 資料過濾
- mutation：改變資料內容的方法
  - 不會執行非同步行為

## 使用課程範例
