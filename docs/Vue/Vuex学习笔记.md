
?>  Vuex学习笔记

- 在`main.js`中进行设置
```js
import Vue from 'vue'
import App from './App'
import store from './store' //将文件引入进来
Vue.config.productionTip = false
new Vue({
el: '#app',
store, //所有的子组件都可以通过this.$store获取
template: '<App/>',
components: { App }
})
```
## 核心概念
- `state`  
1.在组件中获取状态一般使用`computed`
```js
const Counter = {
  template: `<div>{{count}}</div>`,
  computed:{
    count(){
      // 获取count的值
      return this.$store.state.count
    }
  }
}
```
2.mapState辅助函数-方便获取多个状态  
```js
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'
export default {
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,
    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',
    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```
如果这个组件中的这个计算属性和`store`的名字相同，可以使用`ES6的解构赋值`
```js
computed: mapState(['count'])
```
- `getter`  
1.在`store`中使用，对自己的`state`中的状态进行处理，这样不同组件请求这个状态的时候就不要一一再去处理，写重复的代码了；其作用相当于`Vue`中的`computed`一样。他的第一个参数就是`state`
```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    // 在组件中使用this.$store.getters.doneTodos
    // 可以得到对state.todos处理后的结果
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
2.和state拥有自己的`mapState`一样，`getters`也有自己的`mapGetters`辅助函数，用法相同
- `mutations`  
要想改变`store`中的`state`需要使用`mutations`来改变，组件内需要`store.commit`来触发
```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```
第一个参数是`state`，如果想传递多个参数，那么这些参数叫做`mutations`的载荷。载荷应该是一个对象，增加易读性；
```js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
store.commit('increment', {
  amount: 10
})
```
**mutations必须是同步函数**，否则状态改变不可追踪。。
