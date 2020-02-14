# Hello World

通过`Vue`实现简单的`Hello World! -> Bye World!`。

```html
<!DOCTYPE html>
<html>
<head>
  <title>Hello World</title>
</head>
<body>
  <div id="app">
    <!-- 插值表达式访问数据 -->
    {{ content }}
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
      content: 'Hello World!'
    }
  });
  /** 设置任务 */
  setTimeout(function() {
    /** 通过 app.$data 访问数据 */
    app.$data.content = 'Bye World!';
  }, 2000);
</script>
</html>
```