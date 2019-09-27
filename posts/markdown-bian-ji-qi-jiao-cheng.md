---
title: 'markdown编辑器教程'
date: 2019-08-09 17:51:21
tags: [Blog,markdown]
published: true
hideInList: false
feature: /post-images/markdown-bian-ji-qi-jiao-cheng.jpg
---
[简明版教程](http://note.youdao.com/iyoudao/?p=2411)  
[进阶版教程](http://note.youdao.com/iyoudao/?p=2445)
## 标题  

**标题，只需要在这段文字前面加上 #，再在 # 后加一个空格  
增加一个 # ，标题字号相应降低一级**
![标题](https://ouluqiang.github.io//post-images/1569307892987.png)

```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
```
![标题效果](https://ouluqiang.github.io//post-images/1569307931761.png)

## 列表  

### 无序列表  

**你只需要在文字前面加上 - 就可以了；如果你希望是有序列表，在文字前面加上 1. 2. 3. 即可。**
![无序列表](https://ouluqiang.github.io//post-images/1569307967486.png)


```
- 列表1
  - 列表1.1
  - 列表1.2
- 列表2
- 列表3
```

- 列表1
  - 列表1.1
  - 列表1.2
- 列表2
- 列表3


### 有序列表

![有序列表](https://ouluqiang.github.io//post-images/1569308007512.png)


```
1. 列表1
    1. 列表1.1
    2. 列表1.2
2. 列表2
3. 列表3
```

1. 列表1
    1. 列表1.1
    2. 列表1.2
2. 列表2
3. 列表3



## 引用
**如果你需要在文稿中引用一段别处的句子，那么就要用到「引用」格式。  
在引用文字前加上 > 并与文字保留一个字符的空格**

![引用](https://ouluqiang.github.io//post-images/1569308041174.png)

```
	> 记录，成为更好的自己。--有道云笔记 
```

> 记录，成为更好的自己。--有道云笔记 




## 粗体和斜体

**用两个 * 包含一段文本就是粗体的语法；**    
**用一个 * 包含一段文本就是斜体的语法。**  
***注意：字符与文本之间无须空格***

![斜体](https://ouluqiang.github.io//post-images/1569308084185.png)

```
*这是斜体*
```

*这是斜体*

![粗体](https://ouluqiang.github.io//post-images/1569308112324.png)

```
**这是粗体**
```

**这是粗体**


## 链接与图片

**链接：在 Markdown 中，插入链接只需要使用 [显示文本] (链接地址) 即可。**  
**图片：在 Markdown 中，插入图片只需要使用 ![显示文本] (图片链接地址)即可。**  
***注：插入图片的语法和链接的语法很像，只是前面多了一个！***  

![链接](https://ouluqiang.github.io//post-images/1569308139421.png)

```
[链接](https://ouluqiang.github.io)
```

[链接](https://ouluqiang.github.io)

![图片](https://ouluqiang.github.io//post-images/1569308339148.png)

```
![图片连接演示](https://ouluqiang.github.io//images/avatar.png?v=1569307577738)
```

![图片连接演示](https://ouluqiang.github.io//images/avatar.png?v=1569307577738)


## 分割线

**只需要另起一行，连续输入三个星号** ***  **即可分割两段文字内容。**

```
	第一段
	***
	第二段

	第一段
	---
	第二段
```

第一段
***
第二段

第一段
---
第二段




## 代码高亮

![代码高亮](https://ouluqiang.github.io//post-images/1569308458877.png)

![代码高亮代码](https://ouluqiang.github.io//post-images/1569308483292.png)

```
代码高亮
```

## 制作待办事项To-do List
你只需要在待办的事项文本或者清单文本前加上- [ ]、- [x]即可。

**- [] 表示未完成，- [x] 表示已完成。**

***注：键入字符与字符之间都要保留一个字符的空格。***

![已完成](https://ouluqiang.github.io//post-images/1569308519075.png)

```
- [x] 已完成
  - [x] 已完成
  - [x] 已完成  
```

- [x] 已完成
  - [x] 已完成
  - [x] 已完成  

![未完成](https://ouluqiang.github.io//post-images/1569308546303.png)

```
- [ ] 未完成
- [ ] 未完成
```

- [ ] 未完成
- [ ] 未完成

## 高效绘制 流程图、序列图、甘特图、表格
### 流程图
书写graph XX，用以确定将要绘制的流程图及其类型（XX表示流程图类型）。
流程图分为竖向和横向两大类，竖向包括自上而下和自下而上两种顺序，横向包括从右到左和从左到右两种顺序。  
其对应语法分别为：graph TB/graph BT/graph RL/graph LR。

TB - top bottom（自上而下）  
BT - bottom top（自下而上）  
RL - right left（从右到左）  
LR - left right（从左到右）  

![流程图](https://ouluqiang.github.io//post-images/1569308580136.png)

![流程图效果](https://ouluqiang.github.io//post-images/1569308601497.png)



```
graph TD
A[直角四边形]---B{菱形}
B --> C((圆形))
C --> D
C --> E
C -->|插入文本|F
```

### 序列图

![序列图](https://ouluqiang.github.io//post-images/1569308625091.png)

![序列图效果](https://ouluqiang.github.io//post-images/1569308658344.png)


```
sequenceDiagram
loop every day
    A->>B: How are you?

    B->>A: Great!
end
```

### 甘特图
书写 gantt ，用以确定将要绘制的是甘特图。
dateFormat YYYY-MM-DD规定了时间轴，title （标题文本）表示甘特图标题。
需键入section，空一个字符，再输入项目名称的文本（一个section和另一个section之间要空行）。

![甘特图](https://ouluqiang.github.io//post-images/1569308684380.png)

![甘特图效果](https://ouluqiang.github.io//post-images/1569308707886.png)


```
gantt
dateFormat YYYY-MM-DD
title 产品计划书
section 初期阶段
明确需求: 2018-07-01, 9d
section 中期阶段
跟进开发: 2018-07-11, 9d
section 后期阶段
走查测试: 2018-07-22, 9d
```

### 表格

![表格](https://ouluqiang.github.io//post-images/1569308736274.png)


```
    header 1    | header 2    | 第三
    ---         |---          | ---
    row 1 col 1 | row 1 col 2 | 第三个 
    row 2 col 1 | row 2 col 2 | 第三个
```

header 1    | header 2    | 第三
---         |---          | ---
row 1 col 1 | row 1 col 2 | 第三个 
row 2 col 1 | row 2 col 2 | 第三个


## 书写数学公式

![公式](https://ouluqiang.github.io//post-images/1569308765985.png)

![公式图](https://ouluqiang.github.io//post-images/1569308783976.png)

```math
E = mc^2
```



