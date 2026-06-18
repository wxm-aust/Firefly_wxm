---
title: 机器人协会AI组第二阶段题目答案
published: 2023-12-19
pinned: false
description: 机器人协会AI组第二阶段题目答案
tags: [协会,AI组]
category: 管理
draft: false
---
```python
#输出9x9乘法表
for i in range(1,10):
    for j in range(1,i+1):
        print(f"{j}*{i}={i*j}",end=' ')
    print()
"""
知识点：
1.循坏以及循环的嵌套
（1）while循环
（2）for循环
2.格式化语句
（1）format语句
（2）f语句（简洁）
3.range函数
4.print函数的使用
"""
```

```python
#素数之和
def is_prime(x):
    if x < 2:
        return False
    for j in range(2,x):#x可更换为int(x/2)+1或者int(x**0.5)+1
        if x % j == 0:
            return False
    else:
        return True

m,n = int(input("请输入m的值：")),int(input("请输入n的值："))
if m<n:
    prime_sum=0
    for i in range(m,n+1):
        if is_prime(i):
            prime_sum+=i
    print(prime_sum)
else:
    print("请保证m<n!!!")
"""
知识点：
1.素数的定义（素数是除1以外只能被自身整除的自然数）
2.函数的定义以及调用
注意：input函数输入所产生的是字符串格式，要进行数据的格式转换
"""
```

```python
#斐波那契数列
#函数的递归
def Fibonacci1(N):
    if N<2:
        return N
    else:
        return Fibonacci1(N-1)+Fibonacci1(N-2)
#公式法
def Fibonacci2(N):
    sqrt5 = 5**0.5
    fibnN = ((1+sqrt5)/2)**N-((1-sqrt5)/2)**N
    return round(fibnN/sqrt5)

#交换数字
def Fibonacci3(N):
    a,b=0,1
    for i in range(N):
        a,b = b,a+b
    return a

#对比时间
import time
count=40
t1 = time.time()
for i in range(count):
    print(Fibonacci3(i),end=" ")
t2 = time.time()
print(f"\n花费时间为{t2-t1}s")

"""
知识点：
1.函数的递归（不推荐）
2.x,y=y,x等式
3.注重效率
"""

```

![](https://cdn.jsdelivr.net/gh/1944195627wb/images@main/images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202023-12-12%20133118.png)

斐波那契数列公式

```python
#矩阵的转置
#方法1：创建新矩阵
arr1 = [[1,2,3],
        [4,5,6],
        [7,8,9]]
rows = len(arr1)
cols = len(arr1[0])
#用numpy创建新矩阵
#import numpy as np
#arr2 = np.zeros((cols,rows))
#使用for循环和range函数声明二维数组
arr2 = [[0 for j in range(rows)] for i in range(cols)]
for i in range(rows):
    for j in range(cols):
        arr2[j][i] = arr1[i][j]
#方法2：列表推导式
#arr2 = [[arr1[j][i] for j in range(rows)] for i in range(cols)]
print(arr2)

"""
知识点：
1.循环语句
2.列表推导式（简洁）
"""
```

```python
def count1(s, t):
    s = s.lower()
    t = t.lower()
    len_s = len(s)
    len_t = len(t)
    num=0
    for i in range(len_s - len_t + 1):
        if s[i:i + len_t] == t:
            num+=1
    return num

def count2(s,t):
    s = s.lower()
    t = t.lower()
    num = s.count(t)
    return num
s = 'aAbAabaab'
t = 'aab'
print(count2(s,t))

"""
知识点：
1.for break else语句的用法
2.break和continue语句的区别
3.面向对象编程
"""
```

