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

## Filter 自訂畫面資料呈現格式

畫面呈現格式除了 computed 可用外，Filter 可套多數個，就是在數值後面加上 `|` 後面接 Filter name

```html
<div id="app">
  <table class="table">
    <tbody>
      <tr is="row-component" v-for="(item, key) in data" :item="item" :key="key"></tr>
    </tbody>
  </table>
  {{ data[1].cash | currency | dollarSign }}
</div>

<script type="text/x-template" id="row-component">
  <tr>
    <td>{{ item.name }}</td>
    <td>{{ item.cash | currency | dollarSign }}</td>
    <td>{{ item.icash | currency | dollarSign }}</td>
  </tr>
</script>

<script>
// 運用在全局：
  Vue.filter('dollarSign', function (n) {
    return `$ ${n}`;
  });
  Vue.filter('currency', function (n) {
    return n.toFixed(2).replace(/(\d)(?=(\d{3})+\.)/g, '$1,');
  });


var child = {
  props: ['item'],
  template: '#row-component',
  data: function() {
    return {
      data: {}
    }
  },
  filters: {
    // currency: function (n) {
    //   return n.toFixed(2).replace(/(\d)(?=(\d{3})+\.)/g, '$1,');
    // },
    // dollarSign: function (n) {
    //   return `$ ${n}`;
    // }
  },
  mounted: function() {
    console.log('Component:', this)
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
    "row-component": child
  },
  mounted: function() {
    console.log('Vue init:', this)
  }
});
</script>
```

## 無法寫入的資料，用 set 搞定他

一開始沒辦法把資料定得非常完整，特別是有 Ajax 操作，這時候就可透過 set 寫入 Vue 的結構內。

可透過 Vue 的 this 找出一些端倪。

以下範例：

```html
<div id="app">
  <table class="table">
    <tbody>
      <tr is="row-component" v-for="(item, key) in data" :item="item" :key="key"></tr>
    </tbody>
  </table>
</div>

<script type="text/x-template" id="row-component">
  <tr>
    <td>{{ item.name }}</td>
    <td>{{ item.cash }}</td>
    <td>{{ item.icash }}</td>
    <td>
      <span v-if="data.item">{{ data.item.name }}</span>
      <button class="btn btn-sm btn-primary" @click="addData()">寫入資料</button>
    </td>
  </tr>
</script>

<script>
var child = {
  props: ['item'],
  template: '#row-component',
  data: function() {
    return {
      data: {}
    }
  },
  methods: {
    addData: function() {
      // this.data.item = {
      //   name: this.item.name
      // }
      // console.log(this.data, this);

      this.$set(this.data, 'item', {
        name: this.item.name
      });
    }
  },
  mounted: function() {
    console.log('Component:', this)
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
    "row-component": child
  },
  mounted: function() {
    console.log('Vue init:', this)
  }
});
</script>
```

## Mixin 混合其他的元件內容

- mixin 多個混合的概念
- 跟 extends 有一點像，但 extends 是針對單一元件，mixin 則可混合多個元件

以下範例：

```html
<div id="app">
  <table class="table">
    <tbody>
      <tr is="row-component" v-for="(item, key) in data"
        :item="item" :key="key"></tr>
      <tr is="row-component-two" v-for="(item, key) in data"
          :item="item" ></tr>
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

<script>
// mixin 是多個混合的概念

var mixinFilter = {
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
  }
};

var mixinMounted = {
  mounted() {
    console.log('這段是 Mixin 產生')
  }
};

Vue.component('row-component', {
  props: ['item'],
  data: function() {
    return {
      data: {},
    }
  },
  mixins: [mixinFilter, mixinMounted],
});

Vue.component('row-component-two', {
  props: ['item'],
  data: function () {
    return {
      data: 'Twp'
    }
  },
  mixins: [mixinFilter, mixinMounted],
});


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
  mounted: function() {
    console.log('Vue init:', this)
  }
});
</script>
```

## 使用 Directive 開發自己的互動 UI

- 自定義指令
- [官方文件](https://cn.vuejs.org/v2/guide/custom-directive.html)

  ```html
  <div id="app">
    <input type="email" v-model="email" v-focus>
  </div>
  
  <script>
  Vue.directive('focus', {
    // 當被綁定的元素插入到 DOM 中時...
    inserted: function(el) {
      el.focus()
    }
  })
  </script>
  ```

- 實現自定義驗證器

  ```html
  <div id="app">
    <input type="email" v-model="email" v-focus v-validation>
  </div>
  
  <script>
  // 官方文件 https://cn.vuejs.org/v2/guide/custom-directive.html
  Vue.directive('focus', {
    inserted: function(el) {
      el.focus()
    }
  });
  
  Vue.directive('validation', {
    bind: function (el, binding, vnode) { // binding 是 directive 自帶的屬性 // vnode 是 Vue 的虛擬節點
      el.className = 'form-control'
    },
    update: function (el, binding, vnode) {
      var value = el.value;
      var re = /^(([^<>()\[\]\.,;:\s@\"]+(\.[^<>()\[\]\.,;:\s@\"]+)*)|(\".+\"))@(([^<>()[\]\.,;:\s@\"]+\.)+[^<>()[\]\.,;:\s@\"]{2,})$/i;
      console.log(value, re.test(value));
      if (!re.test(value)) {
        el.className = 'form-control is-invalid';
      } else {
        el.className = 'form-control is-valid';
      }
    }
  });
  </script>
  ```

## Directive 細節說明

- 如何透過 directive 操作 v-model 裡的資料？
  - 在 vnode 的 data 的 directives

  ```html
  Vue.directive('validation', {
    bind: function (el, binding, vnode) { // binding 是 directive 自帶的屬性 // vnode 是 Vue 的虛擬節點
      el.className = binding.value.className;
  
      // v-model
      // v-model 值
      var vModel = vnode.data.directives.find(function (item) {
        return item.name === 'model'
      }).expression;
      console.log('vModel', vModel);
      var value = vnode.context[vModel];
      console.log('value', value);
    },
    update: function (el, binding, vnode) {
      // ...
    }
  });
  ```

- 上例的 v-validation 可否傳遞參數？

  ```html
  <div id="app">
    <input type="email" v-model="email" v-focus v-validation="{className: 'form-control'}">
  </div>
  
  <script>
  // 官方文件 https://cn.vuejs.org/v2/guide/custom-directive.html
  Vue.directive('focus', {
    inserted: function(el) {
      el.focus()
    }
  });
  
  Vue.directive('validation', {
    bind: function (el, binding, vnode) { // binding 是 directive 自帶的屬性 // vnode 是 Vue 的虛擬節點
      el.className = binding.value.className;
    },
    update: function (el, binding, vnode) {
      var value = el.value;
      var re = /^(([^<>()\[\]\.,;:\s@\"]+(\.[^<>()\[\]\.,;:\s@\"]+)*)|(\".+\"))@(([^<>()[\]\.,;:\s@\"]+\.)+[^<>()[\]\.,;:\s@\"]{2,})$/i;
      console.log(value, re.test(value));
      if (!re.test(value)) {
        el.className = 'form-control is-invalid';
      } else {
        el.className = 'form-control is-valid';
      }
    }
  });
  </script>
  ```

## 使用外部載入的套件

- Bootstrap Vue *
  - bootstrap v4
  - vue 2.4+
- Vue Strap
  - vue 1.0.8
  - bootstrap v3

vue 不一定要使用 webpack，可以用 Browser

- CLI 版本請加入以下套用 BootstrapVue

  ```html
  <script>
  import BootstrapVue from 'bootstrap-vue'
  Vue.use(BootstrapVue);
  </script>
  ```
