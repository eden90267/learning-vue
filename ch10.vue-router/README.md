# Chap 10. Vue Router

## 使用 Vue Router 及配置路由元件

- main.js：進入點
- router/index.js：Router 配置檔案 (前端路由)
- Vue Component (**.vue)：分頁內容

可看官網[安裝步驟](https://router.vuejs.org/zh/installation.html)

### 安裝步驟

```shell
$ npm i vue-router --save
```

開一個 src/router folder，並 create index.js 檔案

```javascript
// index.js

import Vue from 'vue'
import VueRouter from 'vue-router'
// 官方的元件

import Home from '@/components/HelloWorld'
// 自訂的分頁元件

Vue.use(VueRouter)

export default new VueRouter({
  routes: [
    {
      name: '首頁', // 元件呈現的名稱
      path: '/index', // 對應的虛擬路徑
      component: Home, // 對應的元件
    },
  ],
})
```

將 App.vue 的 HelloWorld 元件標籤改成 router-view 標籤：

```vue
// App.vue

<template>
  <div id="app">
    <img src="./assets/logo.png">
    <!--<HelloWorld/>-->
    <router-view></router-view>
  </div>
</template>

// ...
```

這樣就可透過 router 呈現配置文件所要的內容

## 新增路由路徑及連結

增加一個 page component，來實現切換路由

1. 先將 bootstrap 4 的 css CDN 載入到 index.html
2. create src/components/pages/page.vue

  ```vue
  <template>
    <div class="page">
      <div class="card" style="width: 18rem;">
        <img class="card-img-top" src="" alt="Card image cap">
        <div class="card-body">
          <h5 class="card-title">Card title</h5>
          <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
          <a href="#" class="btn btn-primary">Go somewhere</a>
        </div>
      </div>
    </div>
  </template>
  
  <script>
    export default {
      data() {
        return {};
      }
    }
  </script>
  
  <style scoped>
  
  </style>
  ```

3. router/index.js 增加 page 的路由配置

  ```javascript
  // index.js

  // ...
  
  export default new VueRouter({
    routes: [
      {
        name: '首頁', // 元件呈現的名稱
        path: '/index', // 對應的虛擬路徑
        component: Home, // 對應的元件
      },
      {
        name: '分頁', // 元件呈現的名稱
        path: '/page', // 對應的虛擬路徑
        component: Page, // 對應的元件
      },
    ],
  })
  ```

4. App.vue 增加 navbar 與相關 router-link 標籤設置

  ```vue
  <template>
    <div id="app">
      <nav class="navbar navbar-expand-sm navbar-light bg-light">
        <a class="navbar-brand" href="#">Navbar</a>
  
        <div class="collapse navbar-collapse" id="navbarNav">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <router-link class="nav-link" :to="{name: '首頁'}" active-class="active">Home</router-link>
            </li>
            <li class="nav-item">
              <router-link class="nav-link" to="/page" active-class="active">Page</router-link>
            </li>
          </ul>
        </div>
      </nav>
      <img src="./assets/logo.png">
      <!--<HelloWorld/>-->
      <router-view></router-view>
    </div>
  </template>
  
  <script>
  // import HelloWorld from './components/HelloWorld'
  
  export default {
    name: 'App',
    // components: {
    //   HelloWorld
    // }
  }
  </script>
  
  <style>
  </style>
  ```

## 製作巢狀路由頁面

```vue
// page.vue

<template>
  <div class="page">
    <router-link to="/page/">卡片 1</router-link>
    <router-link to="/page/child2">卡片 2</router-link>
    <router-link to="/page/child3">卡片 3</router-link>
    <div class="card" style="width: 18rem;">
      <router-view></router-view>
    </div>
  </div>
</template>
```

```vue
// child1

<template>
  <div class="child">
    <img class="card-img-top" src="" alt="Card image cap">
    <div class="card-body">
      <h5 class="card-title">Card 1</h5>
      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {};
    }
  }
</script>

<style scoped>

</style>
```

```vue
// child2

<template>
  <div class="child">
    <img class="card-img-top" src="" alt="Card image cap">
    <div class="card-body">
      <h5 class="card-title">Card 2</h5>
      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {};
    }
  }
</script>

<style scoped>

</style>
```

```vue
// child3

<template>
  <div class="child">
    <img class="card-img-top" src="" alt="Card image cap">
    <div class="card-body">
      <h5 class="card-title">Card 3</h5>
      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
      <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {};
    }
  }
</script>

<style scoped>

</style>
```

最後是 router 的巢狀設置：

```javascript
// router/index.js

// ...

export default new VueRouter({
  routes: [
    {
      name: '首頁', // 元件呈現的名稱
      path: '/index', // 對應的虛擬路徑
      component: Home, // 對應的元件
    },
    {
      name: '分頁', // 元件呈現的名稱
      path: '/page', // 對應的虛擬路徑
      component: Page, // 對應的元件
      children: [
        {
          name: '卡片 1', // 元件呈現的名稱
          path: '', // 對應的虛擬路徑
          component: Child, // 對應的元件
        },
        {
          name: '卡片 2', // 元件呈現的名稱
          path: 'child2', // 對應的虛擬路徑
          component: Child2, // 對應的元件
        },
        {
          name: '卡片 3', // 元件呈現的名稱
          path: 'child3', // 對應的虛擬路徑
          component: Child3, // 對應的元件
        },
      ]
    },
  ],
})
```

## 使用動態路由切換頁面 Ajax 結果

```javascript
// route/index.js

// ...

export default new VueRouter({
  routes: [
    {
      name: '首頁', // 元件呈現的名稱
      path: '/index', // 對應的虛擬路徑
      component: Home, // 對應的元件
    },
    {
      name: '分頁', // 元件呈現的名稱
      path: '/page', // 對應的虛擬路徑
      component: Page, // 對應的元件
      children: [
        // ...
        {
          name: '卡片 3', // 元件呈現的名稱
          path: 'child/:id', // 對應的虛擬路徑
          component: Child3, // 對應的元件
        },
      ]
    },
  ],
})
```

安裝 axios, vue-axios：

```shell
$ npm i axios vue-axios --save
```

```vue
// components/pages/child3.vue

// ...

<script>
  export default {
    data() {
      return {};
    },
    created() {
      console.log(this.$route.params.id);
      const id = this.$route.params.id;
      this.$http.get(`https://randomuser.me/api/?seed=${id}`).then(res => {
        console.log(res.data);
      })
    }
  }
</script>

// ...
```

## 命名路由，同一個路徑載入兩個頁面元件

增加一個 menu.vue 元件給 page 頁面才會顯示 卡片 1~3 的 nav 連結：

```vue
// src/components/menu.vue

<template>
  <div class="menu my-3">
    <ul class="nav">
      <li class="nav-item">
        <router-link to="/page/" class="nav-link" active-class="active">卡片 1</router-link>
      </li>
      <li class="nav-item">
        <router-link to="/page/child2" class="nav-link" active-class="active">卡片 2</router-link>
      </li>
      <li class="nav-item">
        <router-link to="/page/child3" class="nav-link" active-class="active">卡片 3</router-link>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    data() {
      return {};
    }
  }
</script>

<style scoped>

</style>
```

page.vue 上方的 連結則可拿掉：

```vue
// src/components/pages/page.vue

<template>
  <div class="page">
    <div class="card" style="width: 18rem;">
      <router-view></router-view>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {};
    }
  }
</script>

<style scoped>

</style>
```

再來修改 route

```javascript
// route/index.js

// ...

export default new VueRouter({
  mode: 'history',
  routes: [
    // ...
    {
      // name: '分頁', // 元件呈現的名稱 // 因為跟 children 的空白 path 有衝突，所以須擇一 name
      path: '/page', // 對應的虛擬路徑
      component: Page, // 對應的元件
      components: { // 載入多個元件用
        default: Page,
        menu: Menu,
      },
      children: [
        {
          name: '卡片 1', // 元件呈現的名稱
          path: '', // 對應的虛擬路徑
          component: Child, // 對應的元件
        },
        {
          name: '卡片 2', // 元件呈現的名稱
          path: 'child2', // 對應的虛擬路徑
          component: Child2, // 對應的元件
        },
        {
          name: '卡片 3', // 元件呈現的名稱
          path: 'child3', // 對應的虛擬路徑
          component: Child3, // 對應的元件
        },
      ]
    },
  ],
})
```

## Vue Router 參數設定

分構建跟實例，請看[官網](https://router.vuejs.org/zh/api/#router-%E6%9E%84%E5%BB%BA%E9%80%89%E9%A1%B9)作對照

- `mode: 'history'`
  - 以 html 5 的方式，可省略掉 # 前綴
  - 要注意這樣不只使用前端路由，後端路由也必須搭配
  - 目前是因為 webpack 有支援這個模式
  - 建議不要使用 history 模式，用戴上 # 即可
- base
  - 應用的根路徑可指定
- linkActiveClass: 預設是 route-link-active，可替換。屬於“模糊”比對
- linkExactActiveClass: 預設是 route-link-active，可替換。屬於“絕對”比對

## 自定義切換路由方法

有時候必須要透過 method 切換路由，譬如 ajax 後會觸發切換路由。請看[官網](https://router.vuejs.org/zh/api/#router-%E5%AE%9E%E4%BE%8B%E6%96%B9%E6%B3%95)

- router.push
- router.replace
  - 不會有歷史紀錄，直接取代目前頁面
- router.go
- router.back

```vue
// src/components/pages/menu.vue

<template>
  <div class="menu my-3">
    <ul class="nav">
      <li class="nav-item">
        <router-link to="/page/" class="nav-link" active-class="active">卡片 1</router-link>
      </li>
      <li class="nav-item">
        <router-link to="/page/child2" class="nav-link" active-class="active">卡片 2</router-link>
      </li>
      <li class="nav-item">
        <router-link to="/page/child3" class="nav-link" active-class="active">卡片 3</router-link>
      </li>
      <li class="nav-item">
        <a href="#" class="nav-link" @click.prevent="updatePath">切換到指定頁面</a>
      </li>
      <li class="nav-item">
        <a href="#" class="nav-link" @click.prevent="beforePath">回到頁面</a>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    data() {
      return {};
    },
    methods: {
      updatePath() {
        // this.$router.push('/page/child2')
        this.$router.replace('/page/child2')
      },
      beforePath() {
        // this.$router.back()
        this.$router.go(-1)
      }
    }
  }
</script>

// ...
```
