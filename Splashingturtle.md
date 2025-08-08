---
timezone: UTC+8
---

# sora

**GitHub ID:** Splashingturtle

**Telegram:** @splashingturtle

## Self-introduction

我是sora，来自苏州，目前在学习Java和web3

## Notes

<!-- Content_START -->
# 2025-08-08

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./404.css">
</head>

<body>
    <div class="wrapper">
        <p class="UI">UIDesignDaily</p>
        <p class="alert">404</p>
        <p class="nr">
            The link you clicked may be broken or the
            <br>page may have been removed
            <br>
            <br>
        </p>
        <p class="dir">Visit the <a href="https://www.douyin.com/" class="home_page">home page</a> or <a
                href="https://www.bilibili.com/" class="contract">contract</a> me</p>
    </div>
</body>

</html>
```css

body{
    background-color: #f3e8f9;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial, sans-serif;
}
p{
    color: white;
}
a{
    color: white;
}
.wrapper{
    background-color: rgb(248, 139, 157);
    padding: 60px 130px 60px 130px;
    border-radius: 10px;
    border: rgb(0, 0, 0),solid;
    text-align: center;
    display: flex;
    flex-direction: column;
    box-shadow: 6px 6px 12px rgba(248, 139, 157, 0.8);
    background: linear-gradient(to right,rgb(240, 115, 199),rgb(255,107,150));
}
.UI{
    font-size: 10px;
    color: white;
    position: relative;
    top: -40px;
}
.alert{
    font-size: 80px;
}
.nr{
    font-size: 8px;
}
.dir{
    font-size: 8px;
}

# 2025-08-07

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial, sans-serif;
}

body {
  background-color: #f3e8f9;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}

.TYPOGRAPHY{
    background-color: rgb(217, 217, 217);
    border-radius: 4px;
    padding: 5px;
    font-weight: bold;
    box-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
    display: inline;
}
.bold{
    font-weight: bold;
}
.ltalic{
    font-style: italic;
}
.underlined{
    text-decoration: underline;
}
.line-through{
    text-decoration: line-through;
}
.HEADINGS{
    display: inline;
    background-color: rgb(217, 217, 217);
    border-radius: 4px;
    padding: 5px;
    font-weight: bold;
    box-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
}
.COLORS{
    display: inline;
    border-radius: 5px;
    font-weight: bold;
    background-color: rgb(217, 217, 217);
    padding: 5px;
    box-shadow: 3px 3px 6px rgba(54, 37, 37, 0.2);
}
.Color-orange{
    color: orange; 
}
.div-orange{
    height: 30px;
    width: 30px;
    background-color: orange;
}
.Color-purple{
    color: purple;
}
.div-purple{
    background-color: purple;
    height: 30px;
    width: 30px;
    border-radius: 100%;
}
.BUTTONS{
    display: inline;
    background-color: rgb(217, 217, 217);
    font-weight: bold;
    padding: 5px;
    border-radius: 5px;
    box-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
}
.Button{
    background-color: purple;
    color: white;
    border: none;
    padding: 3px 10px;
    cursor: pointer;
    border-radius: 5px;
}
.Button:hover{
    background-color: palevioletred;
}
.Button:active{
    background-color: plum;
}
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./basics.css">
</head>

<body>
    <div class="contianer">
        <div class="TYPOGRAPHY">TYPOGRAPHY</div>
        <br>
        <br>
        <p class="bold">make me bold</p>
        <p class="ltalic">make me ltalic</p>
        <p>I'm from the Arial font family!</p>
        <p>And I'm from the Times New Roman font family!</p>
        <p class="underlined">Make this text be underlined</p>
        <p class="line-through">And-put-a-line-through-this-one</p>
        <br>
        <div class="HEADINGS">HEADINGS</div>
        <br>
        <br>
        <h1>This is a &lt;h1&gt; tag</h1>
        <h2>This is a &lt;h2&gt; tag</h2>
        <h3>This is a &lt;h3&gt; tag</h3>
        <br>
        <br>
        <div class="COLORS">COLORS</div>
        <br>
        <br>
        <p class="Color-orange">Color me orange!</p>
        <div class="div-orange"></div>
        <p class="Color-purple">Color me purple!</p>
        <div class="div-purple"></div>
        <br>
        <br>
        <div class="BUTTONS">BUTTONS</div>
        <br>
        <br>
        <button class="Button">Button</button>
    </div>
</body>

</html>

# 2025-08-04

# 前端

## HTML基础标签

**meta标签**

```html
<meta http-equiv="refresh" content="3;http://www.baidu.com">
```

3秒之后，自动跳转到百度页面

**body**标签

- `bgcolor`：设置整个网页的背景颜色。
- `background`：设置整个网页的背景图片。
- `text`：设置网页中的文本颜色。
- `leftmargin`：网页的左边距。IE浏览器默认是8个像素。
- `topmargin`：网页的上边距。
- `rightmargin`：网页的右边距。
- `bottommargin`：网页的下边距。

**p标签的对齐方法**

```html
<p align="">This is a paragraph</p>
```

- `align="属性值"`：对齐方式。属性值包括left center right。

  

**HTML标签是分等级的**，HTML将所有的标签分为两种：

- **文本级标签**：p、span、a、b、i、u、em。文本级标签里只能放**文字、图片、表单元素**。（a标签里不能放a和input）
- **容器级标签**：div、h系列、li、dt、dd。容器级标签里可以放置任何东西。

从学习p的第一天开始，就要牢牢记住：**p标签是一个文本级标签，p里面只能放文字、图片、表单元素**。其他的一律不能放。

错误写法：（尝试把 h 放到 p 里）

```html
	<p>
		我是一个小段落
		<h1>我是一级标题</h1>
	</p>
```

**水平线标签**`<hr />`

水平分隔线（horizontal rule）可以在视觉上将文档分隔成各个部分。在网页中常常看到一些水平线将段落与段落之间隔开，使得文档结构清晰，层次分明。

属性介绍：

- `align="属性值"`：设定线条置放位置。属性值可选择：left right center。
- `size="2"`：设定线条粗细。以像素为单位，内定为2。
- `width="500"`或`width="70%"`：设定线条长度。可以是绝对值（单位是像素）或相对值。如果设置为相对值的话，内定为100%。
- `color="#0000FF"`：设置线条颜色。
- `noshade`：不要阴影，即设定线条为平面显示。若没有这个属性则表明线条具阴影或立体。

**换行标签**`<br />`

如果希望某段文本强制换行显示，就需要使用换行标签。

```html
This <br/> is a para<br/>graph with line breaks
```

**div标签和span标签**

一个换行一个不换行

## css选择器

* class（类选择器）用.，	

class里面可以放多个值

```html
<div class="div1 border"></div>
```

```css
.div {
    
}
.border {
    
}
```

* id选择器

```html
<div id="main">
    
</div>
```

```css
#mian{
    
}
```

* 元素选择器：

```css
body{
    
}
```

* *代表所有元素

```css
*{
}
```

* 选择所有p元素和div元素

```css
div,p{
    
}
```

* 选择div元素内的所有p元素

```css
div p{
}
```

* 选择所有父级是div元素的p元素

```css
div>p{
    
}
```

* 选择所有紧接着div元素之后的p元素

```css
div+p{
    
}
```

属性选择器

```html
<input class="sdfsfsff">
```

```css
[class]{
    
}
```

```css
[class=sdsfsff]{
    
}
```

：hover


# 2025.07.29


<!-- Content_END -->
