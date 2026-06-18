---
title: NumPy函数
published: 2024-02-03
pinned: false
tags: [Python]
category: 学习项目
draft: false
---
## 随机数函数
`np.random.函数`

| 函数 | 说明 |
| :--: | :--: |
| seed(s) | 随机种子 |
| rand(shape) | 创建浮点随机数数组  \[0,1) |
| randn(shape) | 创建正态分布随机数数组 |
| uniform(low,high,shape) | 产生具有均匀分布的数组 |
| normal(loc,scale,size) | 产生具有正态分布的数组,loc均值,scale标准差 |
| poisson(lam,size) | 产生具有泊松分布的数组,lam随机事件发生率 |
| randint(low,high,shape) | 创建随机整数或整数数组 \[low,high) |
| choice(array,shape, replace=False,p) | 从一维array中以概率p抽取元素，形成新数组replace表示是否可以重复使用元素 |
| shuffle(a) | 根据数组a的第1轴进行随排列 |
| permutation(a) | 根据数组a的第1轴产生一个新的乱序数组 |
## 梯度函数
#### `np.gradient(f)`
计算数组f中元素的梯度，当为多维时，返回每个维度梯度
	梯度:连续值之间的变化率，即斜率。
![[梯度例子.png]]
梯度计算：同维度相邻值之差/2 或  自己与相邻值之差 /1 （在角落的时候）
## 统计函数
`np.函数名`

| 函数 | 说明 |
| :--: | :--: |
| min(a)/max(a) | 计算数组a中元素的最小值、最大值 |
| argmin(a)/argmax(a) | 计算数组a中元素最小值、最大值的降一 维后下标 |
| unravel_ index(index, shape) | 根据shape将一维下标index转换成多维下标 |
| ptp(a) | 计算数组a中元素最大值与最小值的差 |
| median(a) | 计算数组a中元素的中位数 |
| sum(a,axis=None) | 根据给定轴计算数组a相关元素之和 |
| mean(a,axis=None) | 根据给定轴计算数组a相关元素的期望 |
| average(a,axs=None,weights) | 根据给定轴计算数组a相关元素的加权平均值 |
| std(a,axis=None) | 根据给定轴axisi算数组a相关元素的标准差 |
| var(a,axis=None) | 根据给定轴axis算数组a相关元素的方差 |


---
axis=None是统计函数的标配参数，用于指定参与计算的维度，默认None是计算整个数组，0是在第一维度上分别计算，1是在第二维度上分别计算，etc..
```python
import numpy as np  
a = np.arange(24).reshape(2,3,4)  
```
生成
```
#[[[ 0  1  2  3]  
#  [ 4  5  6  7]  
#  [ 8  9 10 11]]  
  
# [[12 13 14 15]  
#  [16 17 18 19]  
#  [20 21 22 23]]]
```
0. `print(np.sum(a,axis=None))
	计算结果为`276`，即为所有数的和
1. `print(np.sum(a,axis=0))`
	结果为
	```
	[[12 14 16 18]
	 [20 22 24 26]
	 [28 30 32 34]]
	```
	相当于`0+12` `1+13` ... `10+22` `11+23`
	在最大的分组情况下计算每个元素的和
2. `print(np.sum(a,axis=1))`
		结果为
	```
		[[12 15 18 21]
		 [48 51 54 57]]
	```
	相当于`0+4+8` `1+5+9` ... `14+18+22` `15+19+23`
	将内一层的数组的每个元素求和
3. `print(np.sum(a,axis=2))`
		结果为
	```
		[[ 6 22 38]
		 [54 70 86]]
	```
	相当于`0+1+2+3` `4+5+6+7` ... `16+17+18+19` `20+21+22+23`
	将再内一层的数组的每个元素求和
4. 以此类推...
---

