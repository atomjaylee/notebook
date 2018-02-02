## 属性

* length 获取长度
* constructor 构造函数

```js
console.log(String.constructor); // f Function(){ native code }
```

## 方法

```js
let str = "0123456789";

//  charAt(num) 返回指定下标的字符
console.log(charAt(1)); // => 1

// indexOf(str,beginIndex)
// beginIndex可选，从下标beginIndex处开始匹配
// 匹配到的话，返回该字符首次出现的下标
// 匹配失败的话，返回 -1
console.log(str.indexOf("2")); // => 2

// slice(num1,num2) 截取字符串, 不包含以num2为下标的元素
// substring(num1,num2) 区别是它不支持负数
console.log(str.slice(0, 5)); // => 01234

// substr(num,length)
console.log(str.substr(0, 5)); // => 01234

// toLowerCase() 转为小写
// toUpperCase() 转为大写
console.log("51LOVE".toLowerCase()); // => 51love
console.log("51love".toUpperCase()); // => 51LOVE

// match() 匹配字符
// 支持普通字符和正则匹配
console.log(str.match('atom')) // => null
console.log(str.match("1")); // => ["1", index:1, input:"0123456789"]
console.log(str.match(/1/)); // => ["1", index:1, input:"0123456789"]
console.log(str.match(/1/g)); // => ["1"]

// search(regexp) 检索字符串出现的起始位置，不执行正则的全局匹配g
console.log(str.search(/0/)) // => 0

// replace(regexp/substr,replacement) 将检索到的元素替换成replacement
// replacement 可以是函数
// 如果匹配不到，则返回完整的字符串
console.log(str.replace(/1/g,'99') // => 09923456789
let newStr = str.replace(/1/g,function(val){
  return parseInt(val) + 2
})
console.log(newStr) // => 0323456789

// split(separator,howmany) 将string转化成Array，和join作用相反
// separator 用来分离字符串的标志符号或者字符
// 返回数组的长度限制
console.log(str.split('')) // => ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"]
console.log(str.split('',4)) // => ["0", "1", "2", "3"]
console.log(str.split(' ')) // => ["0123456789"]
console.log(str.split('1')) // => ["0", "23456789"]
```

## 练习

> 查找一个字符串中的所有子串的位置

```js
let str =
  "I think of other ages that floated upon the stream of life and love and death";
let positions = [];
function searchSubstr(str, substr) {
  let pos = str.indexOf(substr);
  while (pos > -1) {
    positions.push(pos);
    pos = str.indexOf(substr, pos + 1);
  }
}
searchSubstr(str, "o");
console.log(positions); // => [8, 11, 29, 37, 51, 64]
```
