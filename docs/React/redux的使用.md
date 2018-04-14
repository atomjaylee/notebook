# redux 的使用步骤

> 因为刚开始学习 redux 的时候，按照社区的建议，直接上了 `redux`,`react-redux` 和`redux-thunk`,但是他们每个扮演的什么角色，还是不太明白，这里做一下总结,这里使用同一个 🌰，**就是更改 store 上的名字**。

## 1. 仅使用 redux

![redux](http://o9z96lbmh.bkt.clouddn.com/redux%E5%8E%9F%E7%90%86%E5%9B%BE.jpeg);

图中，我们可以清晰的看见`react`引入`redux`之后的变化。  
 通俗的讲就是`redux`作为一个外部的数据仓库，来专门为`react`的组件提供数据，不论组件的层级，都能拿到相同的数据，避免了我们平常的`父 -> 子`,`子 -> 父`的情况。

那么整个流程可以理解为:
![redux流程](http://o9z96lbmh.bkt.clouddn.com/redux%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png)

| 组成部分  | 详细解释                                                                    | 备注                                   |
| --------- | --------------------------------------------------------------------------- | -------------------------------------- |
| `action`  | 是 store 唯一的数据来源                                                     | 是一个对象                             |
| `reducer` | 唯一改变数据的途径，为纯函数,结果必须是返回一个新的 store,不能对 store 修改 | 通过 swith 判断 action.type 来决定执行 |
| `store`   | 真正存储数据的地方，可被 dev-tool 以快照的形式获取                          | 会暴露一下方法，让组件获取实时数据     |

### store 暴露的方法(常用的)

* `store.getState()` 获取状态
* `store.subseribe()` 订阅数据(数据发生变化，会触发)
* `store.dispatch()` 派发数据(调用后来改变数据)

### redux 常用的 API

* `combineReducers` 集合 reducer
* `createStore` 管理 action 和 reducer 的关系
* `applyMiddleware` 中间件的嵌入

**代码部分**:

```js
// action.js
export default function(payload) {
  return {
    type: "CHANGE_NAME",
    payload
  };
}
```

```js
// reducer.js
let initState = {
  name: "atomjaylee",
  age: 26
};
export default function(state = initState, action) {
  switch (action.type) {
    case "CHANGE_NAME":
      reutrn Object.assign({},state,...action.payload)
  }
}
```

```js
//index.js
import { createStore } from "redux";
import reducer from "reducer.js";
import createAction from "action.js";
// 创建store
let store = createStore(reducer);

// 触发一次数据更新
store.dispatch(createAction({ name: "XCHARGE" }));

// 订阅
// 每次store数据更新，打印日志
const unsubscribe = store.subseribe(() => {
  console.log(store.getState());
});

// 取消订阅
unsubscribe();
```

### 存在的问题

1.  _写起来太累了，每个组件要想拿到 store 中的数据，必须在每个组件内部新创建一个 store_
2.  _需要联动的数据，必须通过订阅的方式来侦听变化，然后做出操作，很繁琐_
3.  _组件卸载后，还需要手动的去取消订阅的事件_
4.  _...._

## 2.使用 redux + react-redux

> 单纯的引入`redux`会有上面的种种问题，那么我们需要引入`react-redux`这个库，来解决到上面的问题。

`react-redux`的实现是基于 react.context 的，官方对于这个不稳定的 API 是不推荐使用的，现在的`react16.3`版本中已经出现了新 context，官方估计会在`react17`版本中将其移除。

### react-redux 两个 API

1.  `<Provider store={store} />`  
    作为一个顶层组件在存储数据，然后通过`props`的方式下发数据

```js
import { Provider } from "react-redux";
import { createStore } from "redux";
import { render } from "react-dom";
import reducer from "reducer.js";
import App from "app.js";

let store = createStore(reducer);

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("app")
);
```

2.  `connext(mapStateToProps,mapDispatchToProps)(组件名字)`

    > 用作连接 store 并将数据赋给 props 的高级函数，这个地方要引出两个概念，`容器组件`和`展示组件`

    **容器组件**： 用来和 store 连接，处理页面逻辑，主要特点有

    * 除了用来包裹展示组件的`div`，不用样式
    * 向其他的容器组件或展示组件提供行为和数据
    * 用高阶组件`connect`来连接 redux

    **展示组件**： 通过传入的 props，来展示不同样式，不存在数据操作，像一般我们用的`ant-design`等开源库中的组件，大部分都是展示组件

    * 不能自主的拉取和改变数据
    * 只能通过`props`来接收数据
    * 内部 state 只能是用户状态，不能是数据
    * 如没有用户状态，性能优化，生命周期，那么最好使用`函数组件`

```js
// 容器组件
import React from "react";
import { connect } from "react-redux";
import createAction from "action.js";

class SHOW extends React.Component {
  render() {
    return (
      <div>
        我的名字是:{this.props.name}
        <button onClick={this.props.handleChange}>更改名字</button>
      </div>
    );
  }
}
export default connect(
  state => ({
    name: state.name
  }),
  dispatch => ({
    handleChange: () => {
      dispatch(createAction("XCHARGE"));
    }
  })
)(SHOW);
```

### 存在问题

现在调用 store 是很方便了，但是还是存在一个问题的，其实不是`react-redux`的问题，是`redux`的问题，我们每次提交`dispatch(action)`更新数据时，因为 reducer 要求我们 action 必须是一个 Object,目前为止我们使用 createAction 的方式生成 action 并调用。

* 这就意味着在每个使用该功能的 component 中都要写同样的代码。

## 3.引入 Redux-thunk

接着上面的问题，我们最好的方式是将这些逻辑代码封装一下，这样就可以在不同的 component 中调用了。那么我们的思路是这样的：

> `action`是 redux 中唯一的数据来源，那我们只能将这些逻辑封装到`action`中去 ----> 但 reducer 接收的 action 只能是一个普通的对象，而我们封装的却是一个函数，传给 reducer 后，会报错 -----> 如果我们的 reducer 能接收一个函数该多好啊 🤔

那么就需要引入我们的另一个概念了**中间件**, 上面也提到 redux 有个 API`applyMiddleware`,就是接收中间件的。

```js
import { createStore, applyMiddleware } from "redux";
import reducers from "./reducer";
import thunk from "redux-thunk";
import logger from "redux-logger"; // 打印日志的，👍

const store = createStore(
  reducers,
  applyMiddleware(thunk),
  applyMiddleware(logger)
);
```

现在就引入 thunk 功能，引入后就可以传给 reducer 一个函数了。

!> 自己在这个地方被社区误导了，因为只要一提 redux-thunk 总是和异步联系起来，觉得好像要想写异步接口调用必须得上 redux-thunk 似的，说到底 redux-thunk 就是一个能让你在 dispatch 中继续 dispatch 的中间件而已，为了实现更好封装，作者 Dan Abramov 也给出了答案 [英文](https://stackoverflow.com/questions/35411423/how-to-dispatch-a-redux-action-with-a-timeout/35415559#35415559)/[中文](https://juejin.im/entry/57c171e35bbb500063321bd5)

?> 三个概念都已经说完了，结合[Ducks](https://github.com/deadivan/ducks-modular-redux)，给出最佳项目目录和 redux 结构

```
src
├── assets            //静态资源
│   ├── font
│   ├── images
│   ├── js
│   └── style
├── components                                    // 展示组件
│   └── Admin
├── containers                                    // 容器组件(页面)
│   ├── Admin.js
│   └── Login.js
├── index.js                                      // 入口
├── redux                                         // redux管理
|   ├── Index.js(combineReducers)
│   ├── Admin.js
│   ├── Global.js
│   └── login.js
└── routes                                        // 路由配置
    └── index.js
```

* ducks 结构

传统的都是 action 一个文件夹，reducer 一个文件夹，管理起来很麻烦。ducks 则是将它们放到一个文件中

```js
// Admin.js

// action
const CHANGE = "admin/change";

const initState = {
  userInfo: {}
};

// reducer
export default function(state = initState, action) {
  switch (action.type) {
    case CHANGE:
    // ...
    default:
      return state;
  }
}

// create Action
export function changeAction(info) {
  return { type: CHANGE, payload: info };
}

// 封装函数
export const getUserInfo = id => dispatch => {
  axios.get(`/api/xxxx?${id}`).then(({ data }) => {
    dispatch(changeAction(data.info));
  });
};
```

```js
// 容器组件中使用
import { connect } from 'react-redux'
import { getUserInfo } from '../redux/Admin'

class Admin extends react.Component{
  handleClick = () =>{
    this.props.getUserInfo(userId)
  }
  render(){
    return(
      {this.props.userInfo}
      <button onClick={this.handleClick}>点击获取用户信息</button>
    )
  }
}
export default connect(
  state =>({
    userInfo: state.Admin.userInfo
  }),
  {
    getUserInfo
  }
)(Admin)
```
