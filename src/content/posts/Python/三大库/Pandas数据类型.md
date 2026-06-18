---
title: Pandas数据类型
published: 2024-02-05
pinned: false
tags: [Python]
category: 学习项目
draft: false
---
## 一维Series类型
### 创建
#### 直接创建
##### `a = pd.Series([9,8,7,6],index=['a','b','c','d'])`
由一组数据及与之相关的数据索引组成。指定索引 作为第二个参数传入
```
	a    9
	b    8
	c    7
	d    6
	 dtype : int64
```

#### 从字典创建
##### `a = pd.Series({'a':9,'b':8,'c':7})`
不指定索引时字典的键作为索引
```
a	9
b	8
c	7
 dtype : int64
```
在index指定索引时，由索引找键再对应值
![[字典Series.png]]

#### 从ndarray创建
##### `a = pd.Series(np.arange(5),index=np.arange(2,7))`

```
2    0
3    1
4    2
5    3
6    4
	dtype: int32
```
#### 从range函数创建
~~略~~
### 操作
* 可通过`a.index`获得索引和类型，以list形式返回
* 可通过`a.value`获得值和类型，以list形式返回
*  可用`a.name`给Series命名
* 创建Series时除指定索引外还生成自动索引，都可作为访问标志
	```python
	import pandas as pd
	 a = pd.Series([9,8,7,6],['a', 'b', 'c', 'd']) 
	 print(a[1])    #8
	 print(a['c'])  #7
	```
* 切片与ndarray相同
	```python
	import pandas as pd  
	a = pd.Series([9,8,7,6],['a', 'b', 'c', 'd'])
	print(a[:1])  
	print(a[:'c'])
	```
	输出结果是Series类型，都为
	```
	a    9
	b    8
	c    7
	dtype: int64
	```
* 可用ndarray的方法操作Series
	```python
	import pandas as pd  
	import numpy as np  
	a = pd.Series([9,8,7,6],['a', 'b', 'c', 'd'])  
	print(np.square(a))
	```
	结果仍然是Series类型
	```
	a    81
	b    64
	c    49
	d    36
	dtype: int64
	```
* 可用`in`方法判断是否存在相应索引
	```python
	import pandas as pd  
	a = pd.Series([9,8,7,6],['a', 'b', 'c', 'd'])  
	print('d'in a)    #True
	print('x'in a)    #False
	```
* 可用get方法`a.get(key,default)
		若key在a中，则返回对应value
		若key不在a中，则返回default
* Series类型在运算中会自动对齐不同索引的数据
	*  相同索引的value相加
	* 不用索引返回value为NaN
	* 返回的Series的索引数为两个传入的索引数的和 
	![[Series索引.png]]

## 二维DataFrame类型
### 创建
#### 从字典创建DataFrame
```python
import pandas as pd  
  
table = {'环比': [101.5, 101.2, 101.3, 102.0, 100.1],  
         '同比': [120.7, 127.3, 119.4, 140.9, 101.4],  
         '定基': [121.4, 127.8, 120.0, 145.5, 101.6]}  
a = pd.DataFrame(table, index=['北京', '上海', '广州', '深圳', '沈阳']) 

print(a)
```
```
       环比     同比     定基
北京  101.5  120.7  121.4
上海  101.2  127.3  127.8
广州  101.3  119.4  120.0
深圳  102.0  140.9  145.5
沈阳  100.1  101.4  101.6
```
#### 从Series创建DataFrame
![[Series创建DataFrame.png]]