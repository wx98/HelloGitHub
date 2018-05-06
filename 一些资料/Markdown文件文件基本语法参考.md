# 标准 Markdown 语法
*****************************************
## 斜体和加粗


```
*斜斜斜斜斜*   **粗粗粗粗粗**
_斜斜斜斜斜_   __粗粗粗粗粗__
```
*斜斜斜斜斜*   **粗粗粗粗粗**  
_斜斜斜斜斜_   __粗粗粗粗粗__



## 链接和邮件
### 链接:
#### 直接显示的链接   
```
[标题](https://github.com/wx98 "悬停显示内容")
```   
[标题](https://github.com/wx98 "悬停显示内容")   
#### 在文档的任意地方添加链接：
```
这是一段[文本][id]用于解释此链接的添加方法，
  [id]: https://github.com/wx98  "悬停显示内容"
```
这是一段 [文本][23]用于解释此链接的添加方法，
  [23]: https://github.com/wx98  "悬停显示内容"

### 邮件:
```
一个邮件 <wx.98@qq.com> 链接.
```
一个邮件 <wx.98@qq.com> 链接.





## 图片
### 内联(标题可选)
```
![替代文字](图片路径 "悬停显示内容 ")
```
![替代文字](图片路径 "悬停显示内容 ")
###任意位置
```
![替代文字][id]
  [id]: 图片路径 "悬停显示内容"
```
![替代文字][id]
  [id]: 图片路径 "悬停显示内容"

## Headers
```
Setext-style:

标题 1
========

标题 2
--------
```
标题 1
========

标题 2
--------

atx-style (closing #’s are optional):
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


## Lists
Ordered, without paragraphs:
```
1.  Foo
2.  Bar
```
1.  Foo
2.  Bar

Unordered, with paragraphs:
```
*   A list item.

    With multiple paragraphs.

*   Bar
```
*   A list item.

    With multiple paragraphs.

*   Bar


You can nest them:
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
*   Abacus
    * answer
*   Bubbles
    1.  bunk
    2.  bupkis
        * BELITTLER
    3. burper
*   Cunning






## Blockquotes
```
> Email-style angle brackets
> are used for blockquotes.

> > And, they can be nested.

> #### Headers in blockquotes
> 
> * You can quote a list.
> * Etc.
```
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
