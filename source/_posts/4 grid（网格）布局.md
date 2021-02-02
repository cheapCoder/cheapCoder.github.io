---
title: grid（网格）布局
date: 2020-09-30 18:15:01
tags:
- grid
- 布局
- 网格
---


# grid（网格）布局

###### 参考链接 http://www.ruanyifeng.com/blog/2019/03/grid-layout-tutorial.html

## 容器属性

### `display`

采用网格(grid)布局
==注意==: 设为网格布局以后，容器子元素（项目）的float、display: inline-block、display: table-cell、vertical-align和column-*等设置都将失效。

- `grid`：块级容器
- `inline-grid`：行内容器

### `grid-auto-columns / rows`

- 设置自动创建的多余网格的列宽和行高

### `grid-template-rows / columns`

- 指定行列宽度

	- 用像素或百分比

		```
		grid-template-rows: 100px 100px 100px;
	  grid-template-columns: 33.3% 33.3% 33.3%;
		```

	- repeat()

	  ```
		grid-template-rows: repeat（3,100px）
		```

	- auto-fill

		```
	  grid-template-columns: repeat(auto-fill, 100px);
		```

	- fr单位(既fraction)

		```
	  grid-template-columns: 1fr 1fr;
		```

	- minmax(min, max)

- 网格线名称

  注意三行应制定四行网格线

	- [name]

### `grid-template-areas`

- 定义区域，名字即为网格网格名

```
	grid-template-areas: 'a b c'
                      	     'd e f'
                      	     'g h i';
```

  不要利用的区域用"点"(.)表示
  注意，区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为`区域名-start`，终止网格线自动命名为`区域名-end`。

### `grid-row / column-gap`

根据最新标准，上面三个属性名的grid-前缀已经删除，grid-column-gap和grid-row-gap写成column-gap和row-gap，grid-gap写成gap。

- 指定行列的间距
- 合并写法

	- `grid-gap: <grid-row-gap> <grid-column-gap>;`
	- `gap: <row-gap> <column-gap>;`

### `grid-auto-flow`  先行后列

- row / column

	- 不考虑空位，保证先后顺序

		- 链接：https://jsbin.com/wapejok/edit?css,output

- row dense / column dense

	- 尽量紧密排列

		- 链接：https://jsbin.com/xutokec/edit?css,output

### 单元格内容的位置

- `align-items` 垂直方向
- `justify-items` 水平方向
- 合并写法: `place-items: <align-items> <justify-items>;`

### 项目整体在容器内的位置

- `justify-content` 水平方向
- `align-content` 垂直方向
- `place-content`  前两个的合并写法

	- `place-content: <align-items> <justify-items>;`

---

## 项目属性

### 指定项目位置

- `grid-row / column-start / end`

	- 写法

		- 网格线数字
		- 网格线名字
		- span：num
		- 重叠了用`z-index`

	- 简写

		-  `grid-row / column: <start-line> / <end-line>`

### 指定项目区域名

- `grid-area: 区域名`

### 单元格内容的位置(优先)

- `align-self` 垂直方向
- `justify-self` 水平方向
- 合并写法: `place-self: <align-items> <justify-items>;`

![思维导图](../images/4.%20%20grid（网格）布局.png)
