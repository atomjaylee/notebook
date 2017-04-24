## 混合(mixin)

使用`@mixin`声明，使用`@include`使用
```scss
$color: red;
$width: 300px;

@mixin mode-1($opcity:0.5,$height:400px){
  width: $width;
  height: $height;
  background-color: $color;
  opacity:$opcity;
}

.demo(0.8,300px){
  @include mode-1;
}
```
## 继承
将另一个选择器的样式继承过来 ,使用`@extend`
```scss
h1{
  width: 400px;
}
.demo{
  @extend h1;
}
```

## 函数
使用`@function`声明
```scss
$baseFontSize: 10px;
@function px2rem($px){
  @return $px / baseFontSize * 1rem
}
```
## 运算
?> 进行四则运算，注意： 运算符号前后需要空格，否则会报错

## 判断
```scss
$flag: true;
p{
  @if $flag==true{
    color:blue;
  } @else {
    color: red;
  }
}
```
## 循环
使用`@for`声明
`scss`文件：
```scss
  @for $i 1 through 3 {
    .item-#{$i} { width: 10px * $i }
  }
```
`css`文件
```css
.item-1{
  width: 10px;
}
.item-2{
  width: 20px;
}
.item-3{
  width: 30px;
}
```
