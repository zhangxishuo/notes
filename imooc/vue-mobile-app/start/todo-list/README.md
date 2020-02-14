# Todo List

## 基础实现

- `v-model`双向数据绑定
- `v-on:click`监听点击事件
- `v-for`循环列表

```html
<body>
  <div id="app">
    <!-- v-model 双向数据绑定 -->
    <input type="text" v-model="value" />
    <!-- v-on:click 监听点击事件 -->
    <button v-on:click="submit">提交</button>
    <!-- v-for 循环列表 -->
    <ul>
      <li v-for="item in list">{{ item }}</li>
    </ul>
  </div>
</body>
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  /** 新建 Vue 实例 */
  var app = new Vue({
    /** Vue 托管的元素块 */
    el: '#app',
    /** Vue 实例内的数据 */
    data: {
      value: '',
      list: []
    },
    /** Vue 实例内的方法列表 */
    methods: {
      submit: function() {
        /** 通过 this 访问数据 */
        this.list.push(this.value);
        this.value = '';
      }
    }
  });
</script>
```

## 组件化

- `v-bind:content`输入组件

```html
<body>
  <div id="app">
    <!-- v-model 双向数据绑定 -->
    <input type="text" v-model="value" />
    <!-- v-on:click 监听点击事件 -->
    <button v-on:click="submit">提交</button>
    <!-- v-for 循环列表 -->
    <ul>
      <!-- v-bind:content 输入组件的 content -->
      <todo-item v-for="item in list" v-bind:content="item"></todo-item>
    </ul>
  </div>
</body>
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  /** 定义组件配置 */
  var TodoItem = {
    /** 组件输入 */
    props: ['content'],
    /** 组件模板 */
    template: '<li>{{ content }}</li>'
  };
  /** 定义 Vue 全局组件 */
  // Vue.component('TodoItem', TodoItem);
  /** 新建 Vue 实例 */
  var app = new Vue({
    /** Vue 托管的元素块 */
    el: '#app',
    /** Vue 局部组件 */
    components: {
      TodoItem: TodoItem
    },
    /** Vue 实例内的数据 */
    data: {
      value: '',
      list: []
    },
    /** Vue 实例内的方法列表 */
    methods: {
      submit: function() {
        /** 通过 this 访问数据 */
        this.list.push(this.value);
        this.value = '';
      }
    }
  });
</script>
```

## 组件交互

- `:`输入数据
- `@`监听输出事件`(this.$emit('event', data))`

```html
<body>
  <div id="app">
    <!-- v-for 循环列表 -->
    <ul>
      <!-- v-bind:content 输入组件的 content -->
      <todo-item v-for="(item, index) in list" :content="item" :index="index" @delete="remove"></todo-item>
    </ul>
  </div>
</body>
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  /** 定义组件配置 */
  var TodoItem = {
    /** 组件输入 */
    props: ['content', 'index'],
    /** 组件模板 */
    template: '<li @click="click">{{ content }}</li>',
    /** 组件方法 */
    methods: {
      click: function() {
        /** 输出 delete 事件 */
        this.$emit('delete', this.index);
      }
    }
  };
  /** 新建 Vue 实例 */
  var app = new Vue({
    /** Vue 托管的元素块 */
    el: '#app',
    /** Vue 局部组件 */
    components: {
      TodoItem: TodoItem
    },
    /** Vue 实例内的数据 */
    data: {
      value: '',
      list: []
    },
    /** Vue 实例内的方法列表 */
    methods: {
      remove: function(index) {
        this.list.splice(index, 1);
      }
    }
  });
</script>
```