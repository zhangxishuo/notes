# 深入理解 Vue 组件

## 使用组件的细节点

- 组件数据需要使用函数的形式。

```javascript
Vue.component('item', {
  data: function() {
    return {};
  }
});
```

- 获取`DOM`或组件的引用：`ref`，`this.$refs`。

```html
<body>
  <div id="root">
    <p ref="hello" @click="click">Hello World!</p>
  </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  var vm = new Vue({
    el: '#root',
    methods: {
      click: function() {
        console.log(this.$refs.hello.innerHTML);
      }
    }
  });
</script>
```

## 父子组件间的数据传递

- 在子组件中不能直接修改父组件传递过来的数据，父组件 -> 子组件，单向数据流。
- 组件通过属性输入，通过事件输出。

```html
<body>
  <div id="root">
    <counter :count="3" @inc="increase"></counter>
    <counter :count="2" @inc="increase"></counter>
    <p>{{ total }}</p>
  </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  Vue.component('counter', {
    props: ['count'],
    data: function() {
      return {
        number: this.count
      };
    },
    template: '<p @click="click">{{ number }}</p>',
    methods: {
      click: function() {
        this.number ++;
        this.$emit('inc', 1);
      }
    }
  });
  var vm = new Vue({
    el: '#root',
    data: {
      total: 5
    },
    methods: {
      increase: function(value) {
        this.total += value;
      }
    }
  });
</script>
```

## 组件参数校验

- 组件通过`type`配置校验传递参数类型。
- 组件通过`required`配置规定参数是否必须。
- 组件通过`default`配置设置参数默认值。
- 组件通过`validator`自定义参数验证规则。

```html
<body>
  <div id="root">
    <child :content="'Hello World!'"></child>
    <child></child>
  </div>
</body>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script type="text/javascript">
  Vue.component('child', {
    props: {
      content: {
        type: String,
        required: false,
        default: 'Default Value',
        validator: function(value) {
          return value.length > 5;
        }
      }
    },
    template: '<p>{{ content }}</p>'
  });
  var vm = new Vue({
    el: '#root'
  });
</script>
```