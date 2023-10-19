#### 关系选择器

- 后代选择器 `.box a`
- 子代选择器 `article > p`
- 邻接兄弟选择器 （只有最接近的兄弟生效） `h1 + p`
- 通用兄弟选择器 `p ~img`

子元素水平垂直居中最便捷的方式：

```css
.father{
   display:flex
}
.son{
  margin:auto;
  width:200px;
  height:200px
}

```

移动端元素滑动保持元素在中间的属性

```css
.father{
	scroll-snap-type:x mandatory;
    
}
.fater > .son{
	scroll-snap-align:center;        
    }


```

