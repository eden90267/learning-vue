# Chap 08. Vue 常用 API

## 使用 Extend 避免重複造輪

```html
<div id="app">
  <table class="table">
    <tbody>
      <tr is="row-component-one" v-for="(item, key) in data"
        v-if="key % 2"
        :item="item" :key="key"></tr>
      <tr is="row-component-two" v-for="(item, key) in data"
        v-if="(key - 1) % 2"
        :item="item" :key="key"></tr>
    </tbody>
  </table>
</div>

<script type="text/x-template" id="row-component">
  <tr>
    <td>{{ item.name }}</td>
    <td>{{ item.cash | currency | dollarSign }}</td>
    <td>{{ item.icash | currency | dollarSign }}</td>
  </tr>
</script>

<script type="text/x-template" id="row-component-two">
  <tr class="bg-primary text-white">
    <td>{{ item.name }}</td>
    <td>{{ item.cash | currency | dollarSign }}</td>
    <td>{{ item.icash | currency | dollarSign }}</td>
  </tr>
</script>

<script>
// extend 是基礎建構的概念
var newExtend = Vue.extend({
  data: function() {
    return {
      data: {},
      extendData: '這段文字是 extend 得到'
    }
  },
  template: '#row-component',
  filters: {
    dollarSign: function (n) {
      return `$ ${n}`
    },
    currency: function(n) {
      return n.toFixed(2).replace(/./g, function(c, i, a) {
        return i && c !== "." && ((a.length - i) % 3 === 0) ? ',' + c : c;
      });
    }
  },
  mounted: function() {
    console.log('Extend:', this)
  }
})

var childOne = {
  props: ['item'],
  extends: newExtend,
}

var childTwo = {
  props: ['item'],
  data: function() {
    return {
      childTwo: '元件 2', // 不僅有原本的資料，還可以多新的，不會取代掉
      extendData: '這段文字是 元件 得到', // 這個則會複寫掉
    }
  },
  template: '#row-component-two',
  extends: newExtend,
  mounted: function () { // 執行兩次
    console.log('childTwo');
  }
}

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
    "row-component-one": childOne,
    "row-component-two": childTwo,
  },
  mounted: function() {
    console.log('Vue init:', this)
  }
});
</script>
```
