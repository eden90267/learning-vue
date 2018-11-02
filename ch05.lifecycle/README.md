# Chap 05. Vue 的生命週期

## 生命週期

- 生命週期：就是 Hook 的概念，到什麼時間點會觸發
  1. 開始創建
  2. beforeCreate
  3. 數據觀測建立
  4. created
  5. 編譯模板
  6. beforeMount
  7. 模板建立完成，取代原有 HTML DOM
  8. mounted (jQuery 操作)
  9. beforeUpdate
  10. 資料變動
  11. Virtual DOM 重新繪製
  12. updated
  13. beforeDestroy
  14. 移除觀測、子元件、事件監聽
  15. destroyed
- keep alive
  - 包覆的 html 標籤
  - 維持元件的生命週期
  - 適用在 tab 內容，切換的時候不用 destroy 掉內容

範例：

```html
<div id="app" class="text-center">
  <h3>Let's check out the lifecycle of this hur' child.</h3>
  <h4>Check the console!</h4>
  <button @click="toggleShow" class="btn btn-primary">
    <span v-if="isShowing">Hide child</span>
    <span v-else>Show child</span>
  </button>
  <hr>
  <keep-alive>
    <app-child v-if="isShowing"></app-child>
  </keep-alive>
</div>

<script type="text/x-template" id="childarea">
  <div>
    <h4>Hello! {{ text }}</h4>
    <input type="text" class="form-control" v-model="text">
  </div>
</script>

<script>
const Child = {
  template: '#childarea',
  data: function () {
    return {
      text: 'Vue data 資料狀態'
    }
  },
  beforeCreate() {
    console.log(`beforeCreate! ${this.text}`);
  }, 
  created() {
    alert(`created! ${this.text}`);
  }, 
  beforeMount() {
    alert(`beforeMount! ${this.text}`);
  }, 
  mounted() {
    alert(`mounted! ${this.text}`);
  },
  updated () {
    console.log(`updated! ${this.text}`);
  },
  activated () {
    alert(`activated! ${this.text}`);
  },
  deactivated () {
    alert(`deactivated! ${this.text}`);
  },
  beforeDestroy() {
    console.log(`beforeDestroy! ${this.text}`);
  }, 
  destroyed() {
    console.log(`destroyed! ${this.text}`);
  }
};

new Vue({
  el: '#app',
  data() {
    return {
      isShowing: false 
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing;
    }
  },
  components: {
    appChild: Child
  }
});
</script>
```

## 重點說明

- 如果要使用 AJAX 至少要到 CREATED 才能用
- 想要維持資料狀態，可以使用 `<keep-alive>`
  - 包元件，不會觸發 destroy
