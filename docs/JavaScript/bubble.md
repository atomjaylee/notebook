
?> 对 *冒泡-捕获-委派* 做一个笔记

***

 - `冒泡`：自己 ==> document

```HTML
<div class="parent">
  <div class="child">我是子元素</div>
</div>
```
```js
var parent = document.getElementsByClassName('parent')[0]
var child = document.getElementsByClassName('child')[0]
parent.addEventListener('click',function(e){
  console.log('你点击了父元素')
})
child.addEventListener('click',function(e){
  console.log('你点击了子元素')
})
```
![img](/img/冒泡.png)
***

- `捕获`: 自己 ==> 子节点  
```js
/**
 * [事件的第三个参数]
 * @ true  [使用捕获的方式]
 * @ false [使用冒泡的方式（默认）]
 */
child.addEventListener('click',function(e){},true)
```
- ‘委派’ 事件监听器可以分析出是从哪个子节点冒泡上来的事件
> 对父节点做事件委派  
> 1.许多兄弟元素需要绑定同一事件时  
> 2.子元素需要动态的插入删除时

<iframe height='265' scrolling='no' title='RVxJqG' src='//codepen.io/atomjaylee/embed/RVxJqG/?height=265&theme-id=dark&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/atomjaylee/pen/RVxJqG/'>RVxJqG</a> by atomjaylee (<a href='http://codepen.io/atomjaylee'>@atomjaylee</a>) on <a href='http://codepen.io'>CodePen</a>.
</iframe>
