# Vue 实例生命周期

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