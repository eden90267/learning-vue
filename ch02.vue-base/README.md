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
  <img v-bind:src="imgSrc" alt="">
</div>
```

## v-for 動態產生多筆資料於畫面上

可以用這招看資料：

```html
<div id="app">
  <pre>{{list}}</pre>
</div>
```

```html
<div id="app">
  <pre>{{list}}</pre>
  <ul>
    <li v-for="(item, index) in list" v-if="item.age < 25">
      {{index + 1}}. {{item.name}} 年齡是 {{item.age}} 歲
    </li>
  </ul>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    list: [
      {
        name: '小明',
        age: 16
      },
      {
        name: '媽媽',
        age: 38,
      },
      {
        name: '漂亮阿姨',
        age: 24
      }
    ]
  }
})
</script>
```

## 使用 v-on 來操作頁面行為

```html
<div id="app">
  <input type="text" class="form-control" v-model="text">
  <button class="btn btn-primary mt-1" v-on:click="reverseText">反轉字串</button>
  <div class="mt-3">
    {{ newText }}
  </div>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    text: '',
    newText: ''
  },
  // 請在此撰寫 JavaScript
  methods: {
    reverseText: function () {
      this.newText = this.text.split('').reverse().join('');
    }
  }
});
</script>
```

## 預先定義資料狀態的重要性

透過多種方式操作資料內容，預先定義資料結構會非常重要。

要有預先定義資料結構的習慣。尤其是資料內容，不會透過方法幫你定義出來。

```html
<div id="app">
  <input type="text" class="form-control" v-model="text">
  <button class="btn btn-primary mt-1">反轉字串</button>
  <div class="mt-3">
  </div>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    text: '',
    // newText: ''
  },
  methods: {
    reverseText() {
      console.log(this.text);
      this.newText = this.text.split('').reverse().join('');
    }
  }
});
</script>
```

以上 newText 不會因為 methods 內的 func 有填值就會定義出來。

## 透過修飾符，讓 v-on 操作更簡單

針對通用的語法有便利的修飾符。

```html
<div id="app">
  <input type="text" class="form-control" v-model="text">
  <a href="#" class="btn btn-primary mt-1" v-on:click.prevent="reverseText">反轉字串</a>
  <div class="mt-3">
    {{ newText }}
  </div>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    text: '',
    newText: ''
  },
  // 請在此撰寫 JavaScript
  methods: {
    reverseText(event) {
      // console.log(event);
      // event.preventDefault(); // 修飾符就是可以不用加入這段
      this.newText = this.text.split('').reverse().join('');
    }
  }
});
</script>

<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>


```

可查看官網：

- 事件修飾符
  - .stop
  - .prevent
  - .capture
  - .self
  - .once
- 按鍵修飾符
  - .keyup
  - .enter
  - .tab
  - .delete
  - .esc
  - .space
  - .up
  - .down
  - .left
  - .right

官網範例：

```html
<input v-on:keyup.13="submit">

<input v-on:keyup.event="submit">

<!--縮寫：-->
<input @keyup.enter="submit">
```

增加 enter 修飾符：

```html
<div id="app">
  <input type="text" class="form-control" v-on:keyup.enter="reverseText" v-model="text">
  <a v-bind:href="link" class="btn btn-primary mt-1" v-on:click.prevent="reverseText">反轉字串</a>
  <div class="mt-3">
    {{ newText }}
  </div>
</div>
```

縮寫：

```html
<div id="app">
  <input type="text" class="form-control" @keyup.enter="reverseText" v-model="text">
  <a :href="link" class="btn btn-primary mt-1" @click.prevent="reverseText">反轉字串</a>
  <div class="mt-3">
    {{ newText }}
  </div>
</div>
```

- v-bind 可審略
- v-on 可改成 @

## v-class 動態切換 className

```html
<div class="box" :class="{ '要加入的 ClassName' : 判斷式}"></div>
```

範例：

```html
<div id="app">
  <div class="box" :class="{'rotate': isTransform}"></div>
  <hr>
  <button class="btn btn-outline-primary" @click="isTransform = !isTransform">選轉物件</button>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    isTransform: false
  },
});
</script>

<style>
.box {
  transition: transform .5s;
}
.box.rotate {
  transform: rotate(45deg)
}
</style>
```

## computed 運算功能

- 將結果存到變數裡面，並且這變數是可以使用的
- 所有在 computed 裡面的都是 function
- 可運用於畫面之上
- 有使用到 data 內的變數，會在 data 變數更動後觸發，反之不會

以下範例：

```html
<div id="app">
  <input type="text" class="form-control" v-model="text">
  <button class="btn btn-primary mt-1">反轉字串</button>
  <div class="mt-3">
    {{ text }}
  </div>
  {{reverseText}}
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    text: '',
    newText: ''
  },
  // 請在此撰寫 JavaScript
  computed: {
    reverseText: function () {
      return this.text.split('').reverse().join('');
    }
  }
});
</script>
```

## Vue 表單與資料的綁定