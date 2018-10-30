# Chap 03. Todo List

```html
<div id="app">
  <div class="input-group mb-3">
    <div class="input-group-prepend">
      <span class="input-group-text" id="basic-addon1">待辦事項</span>
    </div>
    <input type="text" class="form-control" placeholder="準備要做的任務" v-model="newTodo" @keyup.enter="addTodo">
    <div class="input-group-append">
      <button class="btn btn-primary" type="button" @click="addTodo">新增</button>
    </div>
  </div>
  <div class="card text-center">
    <div class="card-header">
      <ul class="nav nav-tabs card-header-tabs">
        <li class="nav-item">
          <a class="nav-link" :class="{active: visibility === 'all'}" @click="visibility = 'all'" href="#">全部</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" :class="{active: visibility === 'active'}" @click="visibility = 'active'" href="#">進行中</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" :class="{active: visibility === 'completed'}" @click="visibility = 'completed'" href="#">已完成</a>
        </li>
      </ul>
    </div>
    <ul class="list-group list-group-flush text-left">
      <li class="list-group-item" v-for="(item, key) in filteredTodos" @dblclick="editTodo(item)">
        <div class="d-flex" v-if="item.id !== cacheTodo.id">
          <div class="form-check">
            <input type="checkbox" class="form-check-input" v-model="item.completed" :id="item.id">
            <label class="form-check-label"
                   :class="{completed: item.completed}"
                   :for="item.id">
              {{item.title}}
            </label>
          </div>
          <button type="button" class="close ml-auto" @click="removeTodo(item)" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <input type="text" class="form-control"
               v-model="cacheTitle"
               @keyup.esc="cancelEdit"
               @keyup.enter="doneEdit(item)"
               v-if="item.id === cacheTodo.id">
      </li>
      <!--<li class="list-group-item">-->
        <!--<input type="text" class="form-control">-->
      <!--</li>-->
    </ul>
    <div class="card-footer d-flex justify-content-between">
      <span>還有 {{uncompletedCount}} 筆任務未完成</span>
      <a href="#" @click.prevent="clearAllTodo">清除所有任務</a>
    </div>
  </div>
</div>

<script>
var app = new Vue({
  el: '#app',
  data: {
    newTodo: '',
    todos: [],
    cacheTodo: {},
    cacheTitle: '',
    visibility: 'all'
  },
  methods: {
    addTodo: function () {
      var value = this.newTodo.trim();
      var timestamp = Math.floor(Date.now());
      if (!value) {
        return;
      }
      this.todos.push({
        id: timestamp,
        title: value,
        completed: false
      });
      this.newTodo = '';
    },
    removeTodo: function (todo) {
      var vm = this;
      var newIndex = vm.todos.findIndex(function (item, key) {
        return todo.id === item.id;
      });
      this.todos.splice(newIndex, 1);
    },
    editTodo: function (item) {
      this.cacheTodo = item;
      this.cacheTitle = item.title;
    },
    cancelEdit:function () {
      this.cacheTodo = {};
    },
    doneEdit: function (item) {
      item.title = this.cacheTitle;
      this.cacheTitle = '';
      this.cacheTodo = {};
    },
    clearAllTodo: function () {
      this.todos = [];
      this.cacheTodo = {};
      this.cacheTitle = '';
    }
  },
  computed: {
    filteredTodos: function () {
      if (this.visibility === 'all') {
        return this.todos;
      } else if (this.visibility === 'active') {
        return this.todos.filter(function (item) {
          return !item.completed;
        });
      } else if (this.visibility === 'completed') {
        return this.todos.filter(function (item) {
          return item.completed;
        });
      }
      return [];
    },
    uncompletedCount:function () {
      return this.todos.filter(function (item) {
        return !item.completed
      }).length;
    }
  }
});
</script>

<style>
.completed {
  text-decoration: line-through
}
</style>
```