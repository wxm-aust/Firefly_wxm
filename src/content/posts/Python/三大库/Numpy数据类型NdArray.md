---
title: NumPy数据类型NdArray
published: 2024-02-07
pinned: false
tags: [Python]
category: 学习项目
draft: false
---
## NdArray的使用

```python
import numpy as np  
a = np.array([5,1,2,3])  
print(a[1])   #1
```
或创建二维==数组==
```python
import numpy as np  
a = np.array([[5,1,2,3],[4,5,6,7]])  
print(a[0][0])
```

***
###### 区分list列表和numpy数组
list可以存放多种类型，但是想拥有这种灵活性也是要付出一定代价的：为了获得这些灵活的类型，列表中的每一项必须包含各自的类型信息、引用计数和其他信息。
列表包含一个指向指针块的指针，这其中的每一个指针对应一个完整的对象。

NumPy 式数组是类似C语言一样的数组，只能存放同一种类型的数据。缺乏list的灵活性，但是能更有效地存储和操作数据。
包含一个指向连续的数据的指针
***
## NdArray的对象属性
| 属性 | 说明 |
| :--: | :----:|
| .ndim | 获取数组的行数量，即 轴数量 维度数量 或 秩数量 |
| .shape | 获取数组的尺度，即n行m列，返回一个元组which有两个元素 |
| .size | 获取数组元素个数，即n*m的值 |
| .dtype | 获取数组的[数据类型](Numpy数据类型NdArray.md#NdArray的数据类型) |
| .itemsize | 获取数组中每个元素的大小，以字节为单位，可用于计算数组大小 |
## NdArray的数据类型
| 类型 | 说明 |
| :--: | :--: |
| bool | 与C的bool一致 |
| intc | 与C的int一致 |
| intp | 用于索引的整数，与C的ssize_t一致 |
| int8 | 8字节长度整数 \[-128,127] |
| int16 | 16字节长度整数 \[-32768,32767] |
| int32 | 32字节长度整数 \[-2^31,2^31-1]，与C的int一致 |
| int64 | 64字节长度整数 \[-2^63,2^63-1]，与C的longlong一致 |
| uint8 | 8位无符号整数\[0,255] |
| uint16 | 16位无符号整数 \[0,65535] |
| uint32 | 32位无符号整数，与C的unsigned int 一致 |
| uint64 | 64位无符号整数，与C的unsigned long一致 |
| float16 | 16位半精度浮点数，1符号位，5指数，10尾数 |
| float32 | 与C的float一致 |
| float64 | 与C的double一致 |
| complex64 | 复数类型，实部和虚部都是32位浮点数 |
| complex128 | 复数类型，实部和虚部都是64位浮点数 |
##### 数组类型变换
`new_a = a.astype(nwe_type)`
## NdArray的创建
`numpy.empty(shape, dtype = float, order = 'C')`

|参数|描述|
|:---:|:---:|
|shape|数组形状|
|dtype|数据类型 |
|order|有"C"和"F"两个选项,分别代表行优先和列优先 |
##### 指定填充数组
* `np.zeros(10, dtype=int)`创建一个长度为10的数组，数组的值都是0
	`array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])`
* `np.ones((3, 5), dtype=float)`创建一个3×5的浮点数组,数组的值都是1
	```
	array([[ 1., 1., 1., 1., 1.],
		   [ 1., 1., 1., 1., 1.], 
		   [ 1., 1., 1., 1., 1.]])
	```
* `np.full((3, 5), 3.14)`创建一个3×5的浮点型数组，数组的值都是3.14
##### 自动填充数组
* `np.arange(0, 20, 2)`创建数组，从0开始，到20结束，步长为2
	`array([ 0, 2, 4, 6, 8, 10, 12, 14, 16, 18])`
* `np.linspace(0, 1, 5)`创建一个5个元素的数组，以均匀地分配\[0,1]
	`array([ 0. , 0.25, 0.5 , 0.75, 1. ])`
* `np.eye(5)`创建一个5\*5单位矩阵,对角线为1其余为0
	```
	[[1. 0. 0. 0. 0.]
	 [0. 1. 0. 0. 0.]
	 [0. 0. 1. 0. 0.]
	 [0. 0. 0. 1. 0.]
	 [0. 0. 0. 0. 1.]]
	```

* `np.concatenate(name1,name2)`将两个或多个数组合并成一个新的数组
##### 随机数数组
本质是对库函数的调用，详细参照[NumPy随机数函数](NumPy函数.md#随机数函数)的介绍
* 创建一个3×3的、在0~1均匀分布的随机数组成的数组
	 `np.random.random((3, 3))`
* 创建一个3×3的、均值为0、方差为1的正态分布的随机数数组
	`np.random.normal(0, 1, (3, 3))`
* 创建一个3×3的、\[0, 10)区间的随机整型数组
	`np.random.randint(0, 10, (3, 3))`

##### 从其他类型创建数组
`np.array(list/tuple,dtype = None, order = None)`
从列表、元组读入一个数组
* 第一个参数为列表名
* 第二个参数指定读入后的数据类型，若留空则由ndarray自动确定
* 第三个指定计算机内存中的存储元素的顺序，留空行优先

##  NdArray的操作
##### 索引
```python
a = np.array([5,6,2,3,6,8,7,4])  
print(a[1]) #6
```

##### 切片
###### `起始编号:终止编号(不含):步长`
``` python
a = np.array([5,6,2,3,6,8,7,4])  
print(a[1:7:2])    #[6 3 8]
```
多维切片：在需要都输出的维度上单留一个`:`,即可选取整个维度
			最后一个维度可以留两个`:`用以指定步长
### 重塑
```python
a = np.arange(24)
print(a)
	#[ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20        21 22 23]
```

```python
a = np.arange(24).reshape((2,3,4))  
print(a)
```
运行结果
```
	[[[ 0  1  2  3]
	  [ 4  5  6  7]
	  [ 8  9 10 11]]

	 [[12 13 14 15]
	  [16 17 18 19]
	  [20 21 22 23]]]
```


## NdArray的运算
```python
a = np.arange(24).reshape((2,3,4))
a.mean() #获取a的平均值 11.5
a = a/a.mean() #数组除以一个数相当于数组内每个元素都除以这个数
```
设计理念是==将数组看作一个数==，对数组操作就是对每个数操作，如

| 函数 | 说明 |
| :--: | :--: |
| + - * / ** // % |  |
| np.maximum(x,y) | 比较数组xy每个元素并取出较大的数，生成新数组 |
| np.minimum(x,y) | 比较数组xy每个元素并取出较小的数，生成新数组 |
| np.mod(x,y) | 计算x中每个数对y中每个数模运算结果，生成新数组 |
| np.copysign(x,y) | 将数组y中各元素值的符号赋值给数组x对应元素 |
| < > == != | 算术比较，产生布尔型数组 |

| 函数 | 说明 |
| :--: | :--: |
| np.abs(x) | 计算数组各元素的绝对值 |
| np.sqrt(x) |  |
| np.square(x) |  |
| np.log(x) |  |
| np.log10(x) |  |
| np.log2(x) |  |
| np.ceil(x) |  |
| np.floor(x) |  |
| np.rint(x) | 计算各元素四舍五入的值 |
| np.modf(x) | 将数组各元素的小数和整数部分以两个独立数组形式返回 |
| np.cos(x) | 计算余弦 同理正弦正切 |
| np.exp(x) | 计算数组各元素的指数值 |
| np.sign(x) | 计算数组各元素的符号值，1(+), 0,-1(-) |

## NdArry的特性
### 指定输出
通过`out`参数 来指定计算结果的存放位置  或 指定数组的每隔一个元素的位置
```python
import numpy as np  
x = np.arange(5)  
y = np.empty(5)
np.multiply(x, 10, out=y)  
print(y)   #[ 0. 10. 20. 30. 40.]
y = np.zeros(10)
np.power(2, x, out=y[::2])
print(y) #[ 1.  0.  2.  0.  4.  0.  8.  0. 16.  0.]
```
对于较大的数组，通过慎重使用 out 参数将能 够有效节约内存。

## 聚合
通过 `add` 计算前缀和 通过`multiply` 计算前缀积
```python
import numpy as np 
x = np.arange(1, 6)
np.add.reduce(x)     #15
np.multiply.reduce(x)  #120    
```
通过`accumulate`展示计算过程
```python
import numpy as np
x = np.arange(1, 6)
np.add.accumulate(x) # array([ 1, 3, 6, 10, 15])
np.multiply.accumulate(x) # array([ 1, 2, 6, 24, 120])
```
## 广播