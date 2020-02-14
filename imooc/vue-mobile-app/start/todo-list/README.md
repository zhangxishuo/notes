# Todo List

## 基础实现

```html
<!DOCTYPE html>
<html>
<head>
  <title>Todo List</title>
</head>
<body>
  <div id="app">
    <!-- v-model 双向数据绑定 -->
    <input type="text" v-model="value" />
    <!-- v-on:click 监听点击事件 -->
    <button v-on:click="submit">提交</button>
    <!-- v-for 循环列表 -->
    <ul v-for="item in list">
      <li>{{ item }}</li>
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
</html>
```