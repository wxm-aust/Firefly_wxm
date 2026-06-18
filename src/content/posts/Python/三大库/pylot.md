---
title: matplotlib函数
published: 2024-02-07
pinned: false
tags: [Python]
category: 学习项目
draft: false
---
### 绘制直线
#### `plt.plot(x,y,format_string,**kwarg)`
* `x`:X轴数据，传入列表或数组
* `y`:Y轴数据，传入列表或数组
* `format_string`:格式控制，如颜色，风格，标记符号。传入字符串
		![[pltfromat.color.png]]![[pltfromat.style.jpg]]![[pltfromat.mark.png]]
* `**kwarg`:第二组或更多
*  当只有一条时可用不用传入X，以Y索引自动生成X
```python 
#  y=kx 0<x<10  (k=1.5 2.5 3.5 4.5)
import matplotlib.pyplot as plt  
import numpy as np  
  
a = np.arange(10)  
plt.plot(a,a*1.5,'go-',a,a*2.5,'rx',a,a*3.5,'*',a,a*4.5,'b-.')

plt.show()
```

### 绘制饼图
#### `plt.pie(size,explode,label,autopct,shadow,angle`
* `size`指定每一块的大小，传入一个list
* `explode`指定突出显示，突出显示的位置写0.1，传入tup
* `labels`指定每一块对应的标签
* `autopcy`指定显示百分数的方式
* `shadow`传入`False`为二维饼图，`True`为带有阴影效果的饼图
* `angle`表示饼图起始的角度值

### 绘制直方图
#### `plt.hist(array,bin,normed,histtype,color,alpha)`
* `arrat`传入待生成的数组
* `bin`指定直方的个数，即绘制精度

###  绘制雷达图
#### `plt.subplot(***,projection)`
* `***`指定绘图区域
* `projection`给出绘制极坐标的指示
```python 
ax = plt.subplot(111,projection = 'polar')
bars = ax.bar(theta,radii,width,bottom)

for r,bar in zip(radii,bar):
	bar.set_facecolor(plt.cm.virdis(r/10))
	bar.set_alpha(0.5)

plt.show()
```
	[[plt1.png]]

### 绘制散点图(面向对象)
```python 
import numpy as np
import matplotlib.pylot as plt

fig , ax = plt.subplots()
ax.plot(10*np.random.randn(100),10*np.random.randn(100),'o')
ax.set_title('title')

plt.show()
```
