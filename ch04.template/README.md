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

## v-for 與其使用細節

- 物件跟陣列都可使用 v-for，只是索引有所不同
  - 物件的索引就是物件的屬性
- 補上 `:key` 才會讓 DOM 元素跟著置換
- 直接對陣列調整 length 對 v-for 是沒有作用的
- 陣列對索引設置新值是沒有作用的，要用 `Vue.set(target, key, value)`
- 純數字迴圈是可以使用的
- `<template>` 是不會被輸出的
- `v-for` 與 `v-if` 可以一起使用，`v-for` 會先執行
- `v-for` 建議都加上 key

以下範例：

```html
<div id="app">
  <h4>陣列與物件的迴圈</h4>
  <p>請使用 v-for 在陣列與物件上，並且加上索引</p>
  <ul>
    <li v-for="(item, key) in objectData">
      {{ key }} - {{ item.name }} {{ item.age }} 歲
    </li>
  </ul>
  <ul>

  </ul>
  <hr>
  <h4>Key</h4>
  <p>請在範例上補上 key，並觀察其差異</p>
  <ul>
    <li v-for="(item, key) in arrayData" :key="item.age">
      {{ key }} - {{ item.name }} {{ item.age }} 歲 <input type="text">
    </li>
  </ul>
  <button class="btn btn-outline-primary" @click="reverseArray">反轉陣列</button>

  <h4>Filter</h4>
  <p>請製作過濾資料</p>
  <input type="text" class="form-control" v-model="filterText" @keyup.enter="filterData">
  <ul>
    <li v-for="(item, key) in filterArray" :key="item.age">
      {{ key }} - {{ item.name }} {{ item.age }} 歲 <input type="text">
    </li>
  </ul>

  <h4>不能運作的狀況</h4>
  <p>講師說明</p>
  <button class="btn btn-outline-primary" @click="cantWork">無法運行</button>
  <ul>
    <li v-for="(item, key) in arrayData" :key="item.age">
      {{ key }} - {{ item.name }} {{ item.age }} 歲 <input type="text">
    </li>
  </ul>

  <h4>純數字的迴圈</h4>
  <ul>
    <li v-for="item in 10">
      {{ item }}
    </li>
  </ul>

  <h4>Template 的運用</h4>
  <p>請將兩個 tr 一組使用 v-for</p>
  <table class="table">
    <template v-for="item in arrayData">
      <tr>
        <td>{{item.age}}</td>
      </tr>
      <tr>
        <td>{{item.name}}</td>
      </tr>
    </template>
  </table>

  <h4>v-for 與 v-if</h4>
  <p>請加上 v-if 判斷式</p>
  <ul>
    <li v-for="(item, key) in arrayData" v-if="item.age <= 20">
      {{ key }} - {{ item.name }} {{ item.age }} 歲
    </li>
  </ul>

  <h4>v-for 與 元件</h4>
  <p>講師說明</p>
  <ul>
    <list-item :item="item" v-for="(item, key) in arrayData"></list-item>
  </ul>
  <p>注意：現在建議元件使用 v-for 都加上 key。<a href="https://cn.vuejs.org/v2/guide/list.html#%E4%B8%80%E4%B8%AA%E7%BB%84%E4%BB%B6%E7%9A%84-v-for">參考</a></p>
</div>

<script>
Vue.component('list-item', {
  template: `
    <li>
      {{ item.name }} {{ item.age }} 歲
    </li>
  `,
  props: ['item']
});

var app = new Vue({
  el: '#app',
  data: {
    arrayData: [
      {
        name: '小明',
        age: 16
      },
      {
        name: '漂亮阿姨',
        age: 24
      },
      {
        name: '杰倫',
        age: 20
      }
    ],
    objectData: {
      ming: {
        name: '小明',
        age: 16
      },
      auntie: {
        name: '漂亮阿姨',
        age: 24
      },
      jay: {
        name: '杰倫',
        age: 20
      }
    },
    filterArray: [],
    filterText: ''
  },
  methods: {
    // 請在此練習 JavaScript

    // 解答：
    reverseArray: function () {
      this.arrayData.reverse()
      console.log(this.arrayData)
    },
    filterData: function () {
      var vm = this; // (this 是 Vue 的 this)
      vm.filterArray = vm.arrayData.filter(function (item) {
        return item.name.match(vm.filterText); // 有就回傳物件，沒有就回傳 null
      })
    },
    cantWork: function () {
      // 1
      // this.arrayData.length = 0; // 這樣會對 Vue 的 v-for 沒有用，不會因此被消除
      // console.log(this.arrayData); // 數字改成 0，但資料還在。所以不要用這種方式消除陣列

      // 2
      // this.arrayData[0] = {
      //   name: '小強',
      //   age: 99
      // }; // 資料會被修改，但畫面是不變的。所以不能用索引方式操作，要用 Vue.set()
      Vue.set(this.arrayData, 0, {
        name: '小強',
        age: 99
      });
    }
  }
});
</script>
```

## v-if 與其使用細節

- `v-if` 與 `v-else`：相鄰元素可用 `v-else` 表示其他的判斷
- `<template>` 可使用 Vue 指令又不會顯示出來的 html tag 用途，可用來節省 Vue 指令的重複
- `v-else-if` 用來對多個判斷式或模擬量可的判斷做使用
- Vue 切換狀態會用高效能方式切換，有時會有 element 沒有切換到的問題，用 key 可以完善區隔開來
- `v-show` 是透過 `display: none` 方式作切換，`v-if` 是直接移除 DOM 元素

以下範例：

```html
<div id="app">
  <h4>v-if, v-else</h4>
  <p>使用 v-if, v-else 切換物件呈現</p>
  <div class="alert alert-success" v-if="isSuccess">成功!</div>
  <div class="alert alert-danger" v-else>失敗!</div>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="isSuccess" v-model="isSuccess">
    <label class="form-check-label" for="isSuccess">啟用元素狀態</label>
  </div>

  <h4>template 標籤</h4>
  <p>使用 template 切換多數 DOM 呈現</p>
  <table class="table">
    <thead>
      <th>編號</th>
      <th>姓名</th>
    </thead>
    <template v-if="showTemplate">
      <tr>
        <td>1</td>
        <td>安妮</td>
      </tr>
      <tr>
        <td>2</td>
        <td>小明</td>
      </tr>
    </template>
  </table>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="showTemplate" v-model="showTemplate">
    <label class="form-check-label" for="showTemplate">啟用元素狀態</label>
  </div>
  <hr>
  <h4>v-else-if</h4>
  <p>使用 v-else-if 做出分頁頁籤</p>
  <ul class="nav nav-tabs">
    <li class="nav-item">
      <a class="nav-link" href="#" :class="{'active': link === 'a'}" @click.prevent="link = 'a'">標題一</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="#" :class="{'active': link === 'b'}" @click.prevent="link = 'b'">標題二</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" href="#" :class="{'active': link === 'c'}" @click.prevent="link = 'c'">標題三</a>
    </li>
  </ul>
  <div class="content">
    <div v-if="link === 'a'">Ａ</div>
    <div v-else-if="link === 'b'">Ｂ</div>
    <div v-else-if="link === 'c'">Ｃ</div>
  </div>
  <hr>
  <h4>KEY</h4>
  <p>講師說明 Vue 切換狀態可能遇到的問題</p>
  <template v-if="loginType === 'username'">
    <label>Username</label>
    <input class="form-control" placeholder="Enter your username" :key="1">
  </template>
  <template v-else>
    <label>Email</label>
    <input class="form-control" placeholder="Enter your email address" :key="2">
  </template>
  <button class="btn btn-outline-primary mt-3" @click="toggleLoginType">切換狀態</button>
  <hr>
  <h4>v-if 與 v-show</h4>
  <p>講師說明 v-if 與 v-show 的差異</p>
  <div class="alert alert-success" v-show="isSuccess">成功!</div>
  <div class="alert alert-danger" v-show="!isSuccess">失敗!</div>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="isSuccess2" v-model="isSuccess">
    <label class="form-check-label" for="isSuccess2">啟用元素狀態</label>
  </div>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    isSuccess: true,
    showTemplate: true,

    link: 'a',

    loginType: 'username'
  },
  methods: {
    toggleLoginType: function () {
      return this.loginType = this.loginType === 'username' ? 'email' : 'username'
    }
  }
});
</script>
```

## Computed 與 Watch