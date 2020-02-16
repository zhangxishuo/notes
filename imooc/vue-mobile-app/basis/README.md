# Vue 基础精讲

## Vue 实例生命周期

```html
<body>
  <div id="root"></div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  var vm = new Vue({
    el: '#root',
    template: '<div>{{ message }}</div>',
    data: {
      message: 'Hello World!'
    },
    beforeCreate: function() {
      console.log('before create');
    },
    created: function() {
      console.log('created');
    },
    beforeMount: function() {
      console.log(this.$el);
      console.log('before mount');
    },
    mounted: function() {
      console.log(this.$el);
      console.log('mounted');
    },
    beforeDestroy: function() {
      console.log('before destroy');
    },
    destroyed: function() {
      console.log('destroyed');
    },
    beforeUpdate: function() {
      console.log('before update');
    },
    updated: function() {
      console.log('updated');
    }
  });
</script>
```

![](.assets/lifecycle.png)

## Vue 模板语法

- 插值表达式：`{{}}`
- 绑定文本指令：`v-text`
- 绑定代码指令：`v-html`

```html
<body>
  <div id="root">
    {{ message }}
    <div v-text="message"></div>
    <div v-html="message"></div>
  </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  var vm = new Vue({
    el: '#root',
    data: {
      message: 'Hello World!'
    }
  });
</script>
```

## 属性计算与监听器

- 属性计算：`computed`
- 监听器：`watch`

```html
<body>
  <div id="root">
    {{ fullName }}
  </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  var vm = new Vue({
    el: '#root',
    data: {
      firstName: 'Xishuo',
      lastName: 'Zhang'
    },
    /** 计算属性，方法缓存，当依赖的属性不更改时，方法不会重复执行 */
    computed: {
      fullName: function() {
        return this.firstName + ' ' + this.lastName;
      }
    }
  });
</script>
```

## 计算属性的 set 和 get

```html
<body>
  <div id="root">
    {{ fullName }}
  </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  var vm = new Vue({
    el: '#root',
    data: {
      firstName: 'Xishuo',
      lastName: 'Zhang'
    },
    /** 计算属性，方法缓存，当依赖的属性不更改时，方法不会重复执行 */
    computed: {
      fullName: {
        get: function() {
          return this.firstName + ' ' + this.lastName;
        },
        set: function(value) {
          var arr = value.split(' ');
          this.firstName = arr[0];
          this.lastName = arr[1];
        }
      }
    }
  });
</script>
```