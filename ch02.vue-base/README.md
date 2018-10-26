# Chap 02. Base Vue.js

## 應用程式的建立

- Vue 只能一次綁定一個元素，第二個會秀原 html 的內容
  - (所以用 id 綁定元素是比較好的)
- Vue 不能巢狀方式建立應用程式 (new Vue by html el)

  ```html
  <div id="app">
    {{text}}
    <div id="app2">
      {{text2}}
    </div>
  </div>
  
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        text: '這裡是一段話'
      }
    });
    var app2 = new Vue({
      el: '#app2',
      data: {
        text2: '這裡是一段話 2'
      }
    });
  </script>
  ```

## 雙向綁定的資料

### MVVM

- View、ViewModel 與 Model 的軟體設計架構
- Model：資料存取層
- View：佈局和 UI 外觀
- ViewModel：中間綁定的部分
- Vue 非完全的 MVVM 架構，它只是受到 MVVM 啟發

三種資料繫結：

```html
{{message}}
<div v-text="message"></div>
<input type="text" v-model="message">
```

屬性繫結也可用 `v-html` 來輸出標籤。

## MVVM 概念

- View
- Model
- ViewModel：資料繫結器

Vue 以資料狀態操作畫面

## v-bind 動態屬性指令

`v-html`, `v-text` 與 `v-bind` 這種的都統稱為“指令”

`v-bind` 用來更新 html 上面的屬性使用的

```html
<div id="app">
  <img src="{{imgSrc}}" alt="">
</div>


<script>
var app = new Vue({
  el: '#app',
  data: {
    imgSrc: 'https://images.unsplash.com/photo-1479568933336-ea01829af8de?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=d9926ef56492b20aea8508ed32ec6030&auto=format&fit=crop&w=2250&q=80'
  }
})
</script>
```

這樣子會沒有動靜，會沒有做任何替換。那怎麼做？用 `v-bind`：

```html
<div id="app">
  <img v-bind:src="{{imgSrc}}" alt="">
</div>

```