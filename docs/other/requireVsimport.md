# 1.出身不同

- require/exports是泥腿子出身，是js社区中开发者自己设置的规则，并得到大家的广泛认可，如CommonJS，AMD，CMD等；
- import/export是正规军，在TC39制定的ES6中被包含进来；

# 2.出现时间不同

- require/exports是2010年前后出现，后逐渐落寞，CommonJs作为`Node.js`的规范，一直沿用至今，因为现阶段ES6中的import/export无法被浏览器直接兼容，所以需要`babel`来进行编译后再被浏览器识别，我们现阶段编写的import/export最后都会被编译成require/exports;

# 3.写法也有差别

- require/exports只有下面三种简单的方式

  ```javascript
  const fs = require('fs');
  exports.fs = fs;
  module.exports = fs;
  ```

- import/export则写法很多

  ```javascript
  import fs from 'fs'
  import {default as fs} from 'fs'
  import * as fs from 'fs' //将 * 命名成fs
  import {readFile} from 'fs' //导入readFile这个函数
  import {readFile as read} from 'fs' //将导入的函数命名成read
  import fs, {readFile} from 'fs'

  export default fs
  export const fs
  export function readFile
  export { readFile, read }
  export * from 'fs'
  ```

# 4.本质上的差别
ES6的module中导入模块的属性和方法是强绑定`(深拷贝)`，而CommonJs是普通的值传递或者引用传递`(浅拷贝)`
