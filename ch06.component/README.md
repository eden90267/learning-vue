# Chap 06. Component

## 元件概念介紹

- 局部區塊取出，並重複使用
- 舉例
  - header
  - content
  - side
    - component
    - ...
- 元件資料獨立
- 用 Props 傳遞內層資料
- Emit Event 則向外層傳遞資料
- SPA 是透過元件來製作
- SPA 是透過前端模擬路由

## 使用 x-template 建立元件

- 用 x-template 建立元件
- 用 is 掛載來避免 html tag 放置錯誤問題
- 用 props 傳遞資料
- 全域 / 局部註冊

```html
<div id="app">
  <table class="table">
    <thead>
    </thead>
    <tbody>
      <!--<row-component v-for="(item, key) in data" :person="item" :key="key"></row-component>-->
      <tr is="row-component" v-for="(item, key) in data" :person="item" :key="key"></tr>
      <!--<tr v-for="(item, key) in data" :item="item" :key="key">-->
        <!--<td>{{ item.name }}</td>-->
        <!--<td>{{ item.cash }}</td>-->
        <!--<td>{{ item.icash }}</td>-->
      <!--</tr>-->
    </tbody>
  </table>
</div>

<script type="text/x-template" id="rowComponentTemplate">
  <tr>
    <td>{{ person.name }}</td>
    <td>{{ person.cash }}</td>
    <td>{{ person.icash }}</td>
  </tr>
</script>

<script>
  // Vue.component('row-component', {
  //   template: '#rowComponentTemplate',
  //   props: [
  //     'person'
  //   ]
  // });

  // 改成局部註冊
  var child = {
    props: ['person'],
    template: '#rowComponentTemplate'
  };

var app = new Vue({
  el: '#app',
  data: {
    data: [
      {
        name: '小明',
        cash: 100,
        icash: 500,
      },
      {
        name: '杰倫',
        cash: 10000,
        icash: 5000,
      },
      {
        name: '漂亮阿姨',
        cash: 500,
        icash: 500,
      },
      {
        name: '老媽',
        cash: 10000,
        icash: 100,
      },
    ]
  },
  components: {
    'row-component': child
  }
});
</script>
```

## 使用 function return 建構資料格式

Vue 元件的 data 屬性需要使用 function 方式 return 物件，否則將會有編譯錯誤。

```html
<div id="app">
  <counter-component></counter-component>
  <counter-component></counter-component>
  <counter-component></counter-component>
</div>

<script type="text/x-template" id="counter-component">
<div>
  你已經點擊 <button class="btn btn-outline-secondary btn-sm" @click="counter += 1">{{ counter }}</button> 下。
</div>
</script>

<script>
Vue.component('counter-component', {
  data: function () {
    return {
      counter: 0
    }
  },
  template: '#counter-component'
});

var app = new Vue({
  el: '#app',
});
</script>
```

## props 基本觀念

- props name 駝峰式命名 to html attribute 要改成用 '-' 連接分詞與小寫

使用範例：

```html
<div id="app">
  <h2>靜態傳遞</h2>
  <photo img-url="https://images.unsplash.com/photo-1520681080137-2ba442993dd4?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=f9d2aebc72c11a9d5bcbcd66e6ca8cb2&auto=format&fit=crop&w=1350&q=80"></photo>
  <h2>動態傳遞</h2>
  <photo :img-url="url"></photo>
</div>

<script type="text/x-template" id="photo">
<div>
  <img :src="imgUrl" class="img-fluid" alt="" />
  <p>風景照</p>
</div>
</script>

<script>
Vue.component('photo', {
  // 同學請依據課程內容，自行填寫 Props 的寫法
  template: '#photo',
  props: ['imgUrl']
})

var app = new Vue({
  el: '#app',
  data: {
    url: 'https://images.unsplash.com/photo-1522204538344-922f76ecc041?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=50e38600a12d623a878983fc5524423f&auto=format&fit=crop&w=1351&q=80'
  }
});
</script>
```