## 1.polyfill 是什么？

刚开始使用`webpack`的时候，觉得只要使用了`babel`之后，就可以高枕无忧了，但是当编译完后被各种`undefined`整懵逼 🤔。因为`babel`只是负责将类似`箭头函数`,`const/let`,`解构`,`可计算属性名`，`class`等语法糖代码转化 Es5 代码。但是对于像具有浏览器兼容性的`Promise`，`Array.includes()`等方法是不进行编码后的降级处理的。所以出现了报错的情况。

> 那么 babel 不会转化哪些代码呢？ 看这里 👉 [babel-plugin-transform-runtime/definitions.js](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-runtime/src/definitions.js)

那么如何才能让代码再 babel 的时候就对这些做降级处理呢？就是使用`polyfill`

## 2.如何使用 polyfill?

目前使用 polyfill 主要有两种

![polyfll](http://o9z96lbmh.bkt.clouddn.com/use-polyfill.png)

### babel-runtime & babel-plugin-transform-runtime

* 使用方法

  ```js
  // 安装
  yarn add babel-runtime babel-plugin-transform-runtime

  // .babelrc
  {
    "plugins": ["transform-runtime"]
  }
  ```

* 优点
  1.  没有全局污染
  2.  按需引入，不会重复引入
  3.  适合用来开发第三方库
* 缺点
  1.  不是全局生效的，所以实例化对象的方法，如`[1,2,4,5].includes(2)`是依赖 Array.prototype.includes 的，所以仍然无法使用

### babel-polyfill

* 使用方法

```js
yarn add babel-polyfill

// 方式一: webpack.config.js
module.exports ={
  entry: ['babel-polyfill','./src/index.js']
}

// 方式二: index.js
import 'babel-polyfill'
```

* 优点
  1.  一次性抹平所有兼容性问题
* 缺点
  1.  导入后打包的体积会变大很多，好比我们引入 jquery 就是为了使用`$('.app')`来获取元素，太浪费了
  2.  污染了全局对象
  3.  所以不适合框架或者库的开发

### babel-perset-env

之前我们都是安装`babel-preset-es2015`而它和`babel-preset-env`的关系是什么样子的呢？它包含了当前所有 ECMAScript 标准里的最新特性。
![env](http://o9z96lbmh.bkt.clouddn.com/3-1presets-es.png)

* 使用方法

```js
yarn add babel-preset-env

// .babelrc
// targets指定浏览器环境还是node环境
// useBuiltIns: true
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["IE >= 9"]
      },
      "useBuiltIns": true
    }]
  ]
}
```

更详细介绍 👉 [babel-preset-env: a preset that configures Babel for you](http://2ality.com/2017/02/babel-preset-env.html)

* 优点
  1.  按需(指定的浏览器环境)引入 polyfill，减少不必要的 polyfill 引入
* 缺点
  1.  还是会污染全局对象
  2.  虽然减少了体积，但是对于现代浏览器还是太不公平，造成流量浪费

### polyfill.io

上面的都是在 webpack 中做的，虽然够用但是还是面临同样的问题，上述做法会对使用现代浏览器的用户不公平，明明自己的浏览器支持这个属性，还非得引入一个 polyfill 来打补丁。所以 [polyfill.io](https://polyfill.io/v2/docs/) 给出的优化方案是针对浏览器来返回需要的 polyfill。这就 🐂X 了。它们通过浏览器的 UA 返回不同的 polyfill 文件。并且它们还开源了自己实现方案的[polyfill-service](https://github.com/Financial-Times/polyfill-service).

> 但是也是问题的，针对国内五花八门的浏览器，能否精确的识别 UA，并返回正确的 polyfill。如果返回的是错的，怎么补救呢。Dan 也再做过[讨论](https://github.com/facebook/create-react-app/issues/1104)

### es5-shim 和 es6-shim

这个是[`ant-design`](https://ant.design/docs/react/getting-started-cn#%E5%85%BC%E5%AE%B9%E6%80%A7)上在兼容 IE9 部分给出的方案， 但是主要是针对 IE 系列的浏览器。并且不能和`babel-polyfill`和`babel-plugin-transform-runtime`等方案同时使用，不然会报[#6512](https://github.com/ant-design/ant-design/issues/6512)

## 3.目前其他的思路，或者黑魔法？

针对于 IE 浏览器，我们可以使用 IE 浏览器特有的[注释方法](https://css-tricks.com/how-to-create-an-ie-only-stylesheet/)

```HTML
<!--[if IE]>
<script src="https://as.alipayobjects.com/g/component/??console-polyfill/0.2.2/index.js,es5-shim/4.5.7/es5-shim.min.js,es5-shim/4.5.7/es5-sham.min.js,es6-shim/0.35.1/es6-sham.min.js,es6-shim/0.35.1/es6-shim.min.js,html5shiv/3.7.2/html5shiv.min.js,media-match/2.0.2/media.match.min.js" />
  <![endif]-->
<![endif]-->
```
