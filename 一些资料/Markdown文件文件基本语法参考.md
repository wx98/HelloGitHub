# 标准 Markdown 语法

## 1.斜体和加粗
```
*斜斜斜斜斜*   **粗粗粗粗粗**
_斜斜斜斜斜_   __粗粗粗粗粗__
```
示例：
  *斜斜斜斜斜*   **粗粗粗粗粗**  
  _斜斜斜斜斜_   __粗粗粗粗粗__



## 2 链接和邮件
### 2.1链接:
#### 2.1.1直接显示的链接   
```
[标题](https://github.com/wx98 "悬停显示内容")
```   
[标题](https://github.com/wx98 "悬停显示内容")   
#### 2.1.2在文档的任意地方添加链接：
```
这是一段[文本][id]用于解释此链接的添加方法，
  [id]: https://github.com/wx98  "悬停显示内容"
```
这是一段 [文本][23]用于解释此链接的添加方法，
  [23]: https://github.com/wx98  "悬停显示内容"

### 2.2 邮件:
```
一个邮件 <wx.98@qq.com> 链接.
```
一个邮件 <wx.98@qq.com> 链接.





## 3.图片
### 3.1内联(标题可选)
```
![替代文字](图片路径 "悬停显示内容 ")
```
![替代文字](图片路径 "悬停显示内容 ")
### 3.2任意位置
```
![替代文字][id]
  [id]: 图片路径 "悬停显示内容"
```
! [替代文字][id]
  [id]: 图片路径 "悬停显示内容"

## 4.标题
### 4.2样式1:
```

标题 1
========

标题 2
--------
```
示例：
标题 1
========

标题 2
--------

### 4.2样式2:
```
# 标题 1 #
## 标题 2 ##
### 标题 3 ###
#### 标题 4 ####
##### 标题 5 #####
###### 标题 6 ######
```
# 标题 1 #
## 标题 2 ##
### 标题 3 ###
#### 标题 4 ####
##### 标题 5 #####
###### 标题 6 ######


## 5.清单目录
### 5.1有序，没有段落:
```
1.  Foo
2.  Bar
```
示例：
1.  Foo
2.  Bar

### 5.2无序，有段落：
```
*   A list item.

    With multiple paragraphs.

*   Bar
```
示例：
*   A list item.

    With multiple paragraphs.

*   Bar
### 5.3可以嵌套
```
*   Abacus
    * answer
*   Bubbles
    1.  bunk
    2.  bupkis
        * BELITTLER
    3. burper
*   Cunning
```
示例：
*   Abacus
    * answer
*   Bubbles
    1.  bunk
    2.  bupkis
        * BELITTLER
    3. burper
*   Cunning

## 6.引用
```
> 尖括号用于引用
> > 当然他们可以嵌套

> #### 当然引用块里也可以有标题
>
> * 你也可以引用一个列表
> * Etc.
```
示例：
> 尖括号用于引用
> > 当然他们可以嵌套

> #### 当然引用块里也可以有标题
>
> * 你也可以引用一个列表
> * Etc.



## Inline Code
```
`<code>` spans are delimited
by backticks.

You can include literal backticks
like `` `this` ``.
```


## Block Code
Indent every line of a code block by at least 4 spaces or 1 tab.
```
This is a normal paragraph.

    This is a preformatted
    code block.
```




## Horizontal Rules
Three or more dashes or asterisks:
```
---

* * *

- - - -
```
---

* * *

- - - -

## Hard Line Breaks
End a line with two or more spaces:
```
Roses are red,   
