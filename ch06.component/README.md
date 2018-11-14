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

## props 使用上的注意事項

- 單向數據流
  - 傳進來的 props 使用 v-model 會有 console warning
- 資料傳入有時間差，譬如 ajax，子元件使用 props 會跳出 undefined
  - 可運用 v-if 讓元件的產生往後移，與資料完成一起同步繪製
- 物件有傳參考的特性
- `<keep-alive>`

使用範例：

```html
<div id="app">
  <h2>單向數據流</h2>
  <photo :img-url="url"></photo>
  <p>修正單向數據流所造成的錯誤</p>

  <h2 class="mt-3">物件傳參考特性 及 尚未宣告的變數</h2>
  <div class="row">
    <div class="col-sm-4">
      <card :user-data="user" v-if="user.phone"></card>
    </div>
  </div>

  <h2 class="mt-3">維持狀態與生命週期</h2>
  <div class="form-check">
    <input type="checkbox" class="form-check-input" id="isShow" v-model="isShow">
    <label class="form-check-label" for="isShow">Check me out</label>
  </div>
  <div class="row">
    <div class="col-sm-4">
      <keep-alive>
        <keep-card v-if="isShow">
        </keep-card>
      </keep-alive>
    </div>
  </div>
</div>

<script type="text/x-template" id="photo">
<div>
  <img :src="imgUrl" class="img-fluid" alt="" />
  <input type="text" class="form-control" v-model="newUrl">
</div>
</script>


<script type="text/x-template" id="card">
<div class="card">
  <img class="card-img-top" :src="user.picture.large" alt="Card image cap" v-if="user.picture">
  <div class="card-body">
    <h5 class="card-title" v-if="user.name">{{ user.name.first }} {{ user.name.last }}</h5>
    <p class="card-text">{{ user.email }}</p>
  </div>
  <div class="card-footer">
    <input type="email" class="form-control" v-model="user.email">
  </div>
</div>
</script>

<script>
Vue.component('photo', {
  props: ['imgUrl'],
  template: '#photo',
  data: function () {
    return {
      newUrl: this.imgUrl
    }
  }
})

Vue.component('card', {
  props: ['userData'],
  template: '#card',
  data: function () {
    return {
      user: this.userData
    }
  }
});

Vue.component('keepCard', {
  template: '#card',
  data: function() {
    return {
      user: {}
    }
  },
  created: function() {
    var vm = this;
    $.ajax({
      url: 'https://randomuser.me/api/',
      dataType: 'json',
      success: function(data) {
        vm.user = data.results[0];
      }
    });
  }
});

var app = new Vue({
  el: '#app',
  data: {
    user: {},
    url: 'https://images.unsplash.com/photo-1522204538344-922f76ecc041?ixlib=rb-0.3.5&ixid=eyJhcHBfaWQiOjEyMDd9&s=50e38600a12d623a878983fc5524423f&auto=format&fit=crop&w=1351&q=80',
    isShow: true 
  },
  created: function() {
    var vm = this;
    $.ajax({
      url: 'https://randomuser.me/api/',
      dataType: 'json',
      success: function(data) {
        vm.user = data.results[0];
      }
    });
  }
});
</script>
```

## props 型別與預設值

- 有 props 型別傳遞限制與預設值的要求，可透過 props 設定 type 與 default
- 靜態屬性傳入的都會是字串，動態的話則會有所不同 (e.g. number)

使用範例：

```html
<div id="app">
  <h2>Props 的型別</h2>
  <prop-type></prop-type>

  <h2 class="mt-3">靜態與動態傳入數值差異</h2>
  <prop-type :cash="300"></prop-type>
</div>

<script type="text/x-template" id="propType">
<div>
  <input type="number" class="form-control" v-model="newCash">
  {{ typeof(cash)}}
</div>
</script>

<script>
Vue.component('prop-type', {
  props: {
    cash: {
      type: Number,
      default: 300
    }
  },
  template: '#propType',
  data: function() {
    return {
      newCash: this.cash
    }
  }
});

var app = new Vue({
  el: '#app',
  data: {
    cash: 300
  }
});
</script>
```

## emit 向外層傳遞事件

有時候還是不免於要修改到外層資料，就會有像外層傳遞事件的需求

```html
<div id="app">
  <h2>透過 emit 向外傳遞資訊</h2>
  我透過元件儲值了 {{ cash }} 元
  <button class="btn btn-primary" v-on:click="incrementTotal(1)">按我</button>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <hr>
  <button-counter></button-counter>
</div>

<script>
Vue.component('buttonCounter', {
  template: `<div>
    <button @click="incrementCounter" class="btn btn-outline-primary">增加 {{ counter }} 元</button>
    <input type="number" class="form-control mt-2" v-model="counter">
  </div>`,
  data: function() {
    return {
      counter: 1
    }
  },
  methods: {
    incrementCounter: function () {
      this.$emit('increment', Number(this.counter));
    }
  }
});

var app = new Vue({
  el: '#app',
  data: {
    cash: 300
  },
  methods: {
    incrementTotal: function (newNumber) {
      this.cash = this.cash + newNumber;
    }
  }
});
</script>
```

## 元件插槽

- 要替換元件內的部分內容，非整個元件拿來用，透過參數來傳模板會比較不容易，所以 Vue 有元件插槽 Slot 供使用
- 使用 `<slot></slot>` 標籤
- 可指定 name 來做具名的 multiple 替換

使用範例：

```html
<div id="app">
  <h2>沒有插槽可替換的狀態</h2>
  <no-slot-component>
    <p>這是一段另外插入的內容</p>
  </no-slot-component>

  <h2>Slot 基礎範例</h2>
  <single-slot-component>
  </single-slot-component>
  <single-slot-component>
    <p>使用這段取代原本的 Slot。</p>
  </single-slot-component>

  <h2>具名插槽</h2>
  <named-slot-component>
  </named-slot-component>

  <named-slot-component>
    <header slot="header">替換的 Header</header>
    <template slot="footer">替換的 Footer</template>
    <template slot="btn">按鈕內容</template>
    <p>其餘的內容</p>
  </named-slot-component>
</div>

<script type="text/x-template" id="noSlotComponent">
<div class="alert alert-warning">
  <h6>我是一個元件</h6>
  <p>
    這沒有插槽。
  </p>
</div>
</script>

<script type="text/x-template" id="singleSlotComponent">
<div class="alert alert-warning">
  <h6>我是一個元件</h6>
  <slot>
    如果沒有內容，則會顯示此段落。
  </slot>
</div>
</script>

<script type="text/x-template" id="namedSlotComponent">
<div class="card my-3">
  <div class="card-header">
    <slot name="header">這段是預設的文字</slot>
  </div>
  <div class="card-body">
    <slot>
      <h5 class="card-title">Special title treatment</h5>
      <p class="card-text">With supporting text below as a natural lead-in to additional content.</p>
    </slot>
    <a href="#" class="btn btn-primary">
      <slot name="btn">spanGo somewhere</slot>
    </a>
  </div>
  <div class="card-footer">
    <slot name="footer">這是預設的 Footer</slot>
  </div>
</div>
</script>

<script>
Vue.component('no-slot-component', {
  template: '#noSlotComponent',
});

Vue.component('single-slot-component', {
  template: '#singleSlotComponent',
});

Vue.component('named-slot-component', {
  template: '#namedSlotComponent',
});

var app = new Vue({
  el: '#app',
  data: {}
});
</script>
```

## 使用 is 動態切換元件

- 可透過 is 屬性指定元件
- 某些狀況可取代 `v-if`

使用範例：

```html
<div id="app">
  <h2>使用 is 顯示單一組件</h2>
  <primary-component :data="item"></primary-component>
  <div is="primary-component" :data="item"></div>

  <h2 class="mt-3">使用 is 動態切換組件</h2>
  <ul class="nav nav-pills">
    <li class="nav-item">
      <a class="nav-link" :class="{'active': current == 'primary-component'}" href="#" @click.prevent="current = 'primary-component'">藍綠色元件</a>
    </li>
    <li class="nav-item">
      <a class="nav-link" :class="{'active': current == 'danger-component'}" href="#" @click.prevent="current = 'danger-component'">紅色元件</a>
    </li>
  </ul>
  <div class="mt-3">
    <!--<primary-component :data="item" v-if="current === 'primary-component'"></primary-component>-->
    <!--<danger-component :data="item" v-if="current === 'danger-component'"></danger-component>-->
    <div :is="current" :data="item"></div>
  </div>
</div>

<script type="text/x-template" id="primaryComponent">
<div class="card text-white bg-primary mb-3" style="max-width: 18rem;">
  <div class="card-header">{{ data.header }}</div>
  <div class="card-body">
    <h5 class="card-title">{{ data.title }}</h5>
    <p class="card-text">{{ data.text }}</p>
  </div>
</div>
</script>
<script type="text/x-template" id="dangerComponent">
<div class="card text-white bg-danger mb-3" style="max-width: 18rem;">
  <div class="card-header">{{ data.header }}</div>
  <div class="card-body">
    <h5 class="card-title">{{ data.title }}</h5>
    <p class="card-text">{{ data.text }}</p>
  </div>
</div>
</script>

<script>
Vue.component('primary-component', {
  props: ['data'],
  template: '#primaryComponent',
});
Vue.component('danger-component', {
  props: ['data'],
  template: '#dangerComponent',
});

var app = new Vue({
  el: '#app',
  data: {
    item: {
      header: '這裡是 header',
      title: '這裡是 title',
      text: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Enim perferendis illo reprehenderit ex natus earum explicabo modi voluptas cupiditate aperiam, quasi quisquam mollitia velit ut odio vitae atque incidunt minus?'
    },
    current: 'primary-component'
  }
});
</script>
```

## Homework

```html
<!DOCTYPE html>
<html>
  <head>
    <title>作業練習：元件變換</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link href="images/favicon.ico" rel="shortcut icon">
    <meta charset="UTF-8">
    <link rel="stylesheet" href="/stylesheets/all.css">
    <script defer src="https://use.fontawesome.com/releases/v5.0.8/js/solid.js" integrity="sha384-+Ga2s7YBbhOD6nie0DzrZpJes+b2K1xkpKxTFFcx59QmVPaSA8c7pycsNaFwUK6l" crossorigin="anonymous"></script>
    <script defer src="https://use.fontawesome.com/releases/v5.0.8/js/regular.js" integrity="sha384-t7yHmUlwFrLxHXNLstawVRBMeSLcXTbQ5hsd0ifzwGtN7ZF7RZ8ppM7Ldinuoiif" crossorigin="anonymous"></script>
    <script defer src="https://use.fontawesome.com/releases/v5.0.8/js/fontawesome.js" integrity="sha384-7ox8Q2yzO/uWircfojVuCQOZl+ZZBg2D2J5nkpLqzH1HY0C1dHlTKIbpRz/LG23c" crossorigin="anonymous"></script>    <script src="/javascripts/vendor.js"></script>

  </head>

  <body class="bg-lighter">
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <a class="navbar-brand" href="/">Vue.js 練習手冊</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarText" aria-controls="navbarText" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarText">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item">
        <a class="nav-link" href="/basic/dev.html">基礎教學</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="/template/basic.html">模板指令</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="/components/basic.html">組件</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="/api/vue_set.html">常用 API</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="/es6/let_const.html">ES6</a>
      </li>

    </ul>
  </div>
</nav>
    <div class="container my-5">
      <div class="row">

          <div class="col-md-3">

<div>
  <h6 class="text-capitalize text-muted">components</h6>


      <a href="/components/basic.html"  class="btn btn-block btn-link">
        基礎元件
      </a>



      <a href="/components/function_return.html"  class="btn btn-block btn-link">
        元件必須使用 function return
      </a>



      <a href="/components/prop_basic.html"  class="btn btn-block btn-link">
        由外到內的資料傳遞
      </a>



      <a href="/components/prop_adv.html"  class="btn btn-block btn-link">
        Props 注意事項
      </a>



      <a href="/components/prop_type.html"  class="btn btn-block btn-link">
        Props 的型別
      </a>



      <a href="/components/emit.html"  class="btn btn-block btn-link">
        向外層傳送事件(Data)
      </a>



      <a href="/components/slot.html"  class="btn btn-block btn-link">
        Slot 插槽替換
      </a>



      <a href="/components/is.html"  class="btn btn-block btn-link">
        動態組建切換
      </a>



      <a href="/components/eventbus.html"  class="btn btn-block btn-link">
        Event Bus
      </a>



      <a href="/components/homework_component.html"  class="btn btn-block btn-primary active">
        作業練習：元件變換
      </a>


</div>
          </div>

        <div class="col-md-9 post">
          <!-- 練習開始 -->
          <h1 class="mt-0 text-muted">作業練習：元件變換</h1>

<h3>模板練習作業：Ajax 資料內容呈現</h3>
<p>本作業一定要用到元件。</p>
<div class="alert alert-secondary">
  <p>提示：</p>
  <ol class="mb-0">
    <li>將資料內容透過 "元件" 呈現</li>
    <li>製作城市過濾選項</li>
    <li>將內層資料透過 emit 傳遞到外層 (作為另一個關注城市的呈現)</li>
    <li>依據不同污染呈現不同色彩</li>
    <li>加分題：透過 localStorage 儲存上次關注的城市</li>
  </ol>
</div>







<div id="app">
  <select name="" id="" class="form-control mb-3">
    <option value="">--- 請選擇城市 ---</option>
    <option :value="option" v-for="option in options">{{option}}</option>
  </select>
  <div>
    <h4>關注城市</h4>
    <div class="card-columns">
      <card :custom-class="statusClass(item.Status)" v-for="item in starData" :key="item.SiteName + item.County">
        <template slot="header">
          {{item.County}} - {{item.SiteName}}
          <a href="#" class="float-right" @click.prevent="onUnstar(item)"><i class="fas fa-star"></i></a>
        </template>
        <Template slot="body">
          <ul class="list-unstyled">
            <li>AQI 指數: {{item.AQI}}</li>
            <li>PM2.5: {{item['PM2.5']}}</li>
            <li>說明: {{item.Status}}</li>
          </ul>
          {{item.PublishTime}}
        </Template>
      </card>
    </div>
  </div>

  <hr>
  <div class="card-columns">
    <card :custom-class="statusClass(item.Status)" v-for="item in unstarData" :key="item.SiteName + item.County">
      <template slot="header">
        {{item.County}} - {{item.SiteName}}
        <a href="#" class="float-right" @click.prevent="onStar(item)"><i class="far fa-star"></i></a>
      </template>
      <Template slot="body">
        <ul class="list-unstyled">
          <li>AQI 指數: {{item.AQI}}</li>
          <li>PM2.5: {{item['PM2.5']}}</li>
          <li>說明: {{item.Status}}</li>
        </ul>
        {{item.PublishTime}}
      </Template>
    </card>
  </div>
</div>

<script type="text/x-template" id="cardTemplate">
  <div class="card" :class="[customClass]">
    <div class="card-header">
      <slot name="header"></slot>
    </div>
    <div class="card-body">
      <slot name="body"></slot>
    </div>
  </div>
</script>

<script>
// API 來源
// https://opendata.epa.gov.tw/Data/Contents/AQI/

Vue.component('card', {
  template: '#cardTemplate',
  props: ['customClass', 'star']
});

var app = new Vue({
  el: '#app',
  data: {
    data: [],
    location: [],
    stared: [],
    filter: '',
  },
  // 請在此撰寫 JavaScript
  methods: {
    getData() {
      const vm = this;
      const api = 'http://opendata2.epa.gov.tw/AQI.json';

      // 使用 jQuery ajax
      $.get(api).then(function( response ) {
        vm.data = response;
        vm.data.map(function (item) {
          return {star: false, ...item};
        })
      });
    },
    onStar(item) {
      const index = this.data.findIndex(function (each) {
        return item.SiteName === each.SiteName
      });
      Vue.set(this.data, index, {...item, star: true})
    },
    onUnstar(item) {
      const index = this.data.findIndex(function (each) {
        return item.SiteName === each.SiteName && item.County === each.County;
      });
      Vue.set(this.data, index, {...item, star: false})
    },
    statusClass: function (status) {
      switch (status) {
        case '良好':
          return '';
        case '普通':
          return 'status-aqi2';
        case '對敏感族群不健康':
          return 'status-aqi3';
        case '對所有族群不健康':
          return 'status-aqi4';
        case '非常不健康':
          return 'status-aqi5';
        case '危害':
          return 'status-aqi6';
        default:
          return '';
      }
    },
  },
  computed: {
    options: function () {
      const counties = this.data.map(function (item) {
        return item.County;
      });
      return [...new Set(counties)];
    },
    starData: function () {
      return this.data.filter(function (item) {
        return item.star
      });
    },
    unstarData: function () {
      return this.data.filter(function (item) {
        return !item.star
      });
    }
  },
  mounted() {
    this.getData();
  },
});
</script>
<!--
狀態對應表
'良好',
'status-aqi2' '普通',
'status-aqi3' '對敏感族群不健康',
'status-aqi4' '對所有族群不健康',
'status-aqi5' '非常不健康',
'status-aqi6' '危害'
-->
<style>
.status-aqi2 {
  background-color: #ffff00;
}
.status-aqi3 {
  background-color: #ff7e00;
}
.status-aqi4 {
  background-color: #ff0000;
}
.status-aqi5 {
  background-color: #8f3f97;
}
.status-aqi6 {
  background-color: #7e0023;
}
</style>


        </div>
      </div>
    </div>
  </body>
</html>
```
