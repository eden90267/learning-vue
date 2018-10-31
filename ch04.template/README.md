# Chap 04. 進階模板語法介紹

## 模板資料細節說明

- 字串用兩個大括號 `{{}}` 來呈現
- 要呈現 html 結構請用 `v-html`
  - 不過非常危險，有 XSS 攻擊顧慮，最好是可信任的內容再使用
- 單向綁定的方式：`v-once`，之後有變更不會跟著綁定
- `{{}}` 裡面可輸入表達式
- `v-bind` 是針對 html element 的屬性進行資料綁定

以下範例：

```html
<div id="app">
  <h4>字串</h4>
  {{ text }}
  <input type="text" class="form-control" v-model="text">

  <h4 class="mt-3">原始 HTML</h4>
  {{ rawHtml }}
  <p v-html="rawHtml">請在此加入原始 HTML 結構</p>
  <p><a href="https://cn.vuejs.org/v2/api/#v-html">額外注意事項</a></p>

  <h4 class="mt-3">單次綁定</h4>
  <div v-once="text">請將此欄位改為單次綁定</div>

  <h4 class="mt-3">表達式</h4>
  <p>練習不同的表達式</p>
  {{text + rawHtml}}
  {{text.split('').reverse().join('')}}
  {{number1 * number2}}

  <hr>
  <h4 class="mt-3">HTML 屬性</h4>
  <p :id="htmlId">請綁定上 ID</p>
  
  <input type="text" class="form-control" placeholder="請在此加上動態 disabled" :disabled="isDisabled">
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    text: '這是一段文字',
    rawHtml: `<span class="text-danger">紅色文字</span>`,
    number1: 100,
    number2: 300,
    htmlId: 'HTMLID',
    isDisabled: true
  },
});
</script>
```

## 動態切換 ClassName 及 Style 多種方法

- `:class="{'className': boolean}"`
  - 可插入一個以上的值
  - 也可將物件透過 Vue 來操作的方式 `:class="objectClass"`
  - 陣列寫法：`:class="['btn-outline-primary', 'active']"`
    - 比較不確定的用法，不是單純 true/false
- 插入行內樣式：`:style="{key: value}"`
  - 將會自動加上 prefix

以下範例：

```html
<div id="app">
  <h4>物件寫法</h4>
  <div class="box" :class="{'rotate': isTransform, 'bg-danger': boxColor}"></div>
  <p>請為此元素加上動態 className</p>
  <hr>
  <button class="btn btn-outline-primary" v-on:click="isTransform = !isTransform">選轉物件</button>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="classToggle1" v-model="boxColor">
    <label class="form-check-label" for="classToggle1">切換色彩</label>
  </div>
  <hr>
  <h5>物件寫法 2</h5>
  <div class="box" :class="objectClass"></div>
  <p>請將此範例改為 "物件" 寫法</p>
  <hr>
  <button class="btn btn-outline-primary" @click="objectClass.rotate = !objectClass.rotate">選轉物件</button>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="classToggle2" v-model="objectClass['bg-danger']">
    <label class="form-check-label" for="classToggle2">切換色彩</label>
  </div>
  <hr>
  <h4>陣列寫法</h4>
  <button class="btn" :class="arrayClass">請操作本元件</button>
  <p>請用陣列呈現此元件 className</p>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="classToggle3" v-model="arrayClass" value="btn-outline-primary">
    <label class="form-check-label" for="classToggle3">切換樣式</label>
  </div>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="classToggle4" v-model="arrayClass" value="active">
    <label class="form-check-label" for="classToggle4">啟用元素狀態</label>
  </div>
  <hr>
  <h4>綁定行內樣式</h4>
  <p>請用不同方式綁定以下行內樣式</p>
  <div class="box" :style="{backgroundColor: 'red'}"></div>
  <div class="box" :style="styleObject"></div>
  <div class="box" :style="[styleObject, styleObject2]"></div>
  <hr>
  <h5>自動加上 Prefix (每個版本結果不同)</h5>
  <div class="box" :style="styleObject3"></div>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    isTransform: false,
    boxColor: false,
    objectClass: {
      'rotate': false,
      'bg-danger': false,
    },

    // Array 操作
    arrayClass: [],

    // 行內樣式
    // 使用駝峰式命名
    styleObject: {
      backgroundColor: 'red',
      borderWidth: '5px'
    },
    styleObject2: {
      boxShadow: '3px 3px 5px rgba(0, 0, 0, 0.16)'
    },
    styleObject3: {
      userSelect: 'none'
    }
  },
});
</script>

<style>
.box {
  transition: all .5s;
}
.box.rotate {
  transform: rotate(45deg)
}
</style>
```
