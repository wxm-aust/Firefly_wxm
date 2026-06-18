---
title: 机器人协会AI组第三阶段题目答案
published: 2024-01-07
pinned: false
description: 机器人协会AI组第三阶段题目答案
tags: [协会,AI组]
category: 管理
draft: false
---
```python
# 排序
# 冒泡排序
def bubbleSort(s: list, if_reverse: bool):
    flag = True
    i = 1
    while i < len(s) and flag:
        flag = False
        for j in range(len(s) - i):
            if (if_reverse and s[j + 1] < s[j]) or (not if_reverse and s[j + 1] > s[j]):
                s[j], s[j + 1] = s[j + 1], s[j]
                flag = True
        i += 1
    return s

s = [1,4,7,2,5,8,3,6,9]
if_reverse = True
print(bubbleSort(s,if_reverse))

"""
1.尝试用多种方法（用冒泡举例）
2.or and关键词的使用
3.bool类型
"""
```

```python
#字符种类的统计
def count(string):
    num1,num2,num3,num4=0,0,0,0
    for char in string:
        if ord('A')<=ord(char)<=ord('z'):
            num1+=1
        elif char==' ':
            num2+=1
        elif ord('1')<=ord(char)<=ord('9'):
            num3+=1
        else:
            num4+=1
    print(f"字母数:{num1},空格数:{num2},数字数:{num3},其他字符数:{num4}")

s='asadzc 289 ad .,p./'
count(s)

"""
知识点：
1.ord函数的使用(输出的为对应的编码)
2.条件分支语句的使用(if elif...else)
"""
```

```python
#最大公约数和最小公倍数
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a

def lcm(a, b):
    return a * b // gcd(a, b)

m = 24
n = 18
print(gcd(m,n),lcm(m,n))

"""
知识点：
1.辗转相除法
2.最大公约数和最小公倍数的关系
"""
```

```python
#找出字符频数最高
def count(s):
    s_dict = dict()
    for char in s:
        if char in s_dict:
            s_dict[char] +=1
        else:
            s_dict[char]=1
    #找出出现次数最多的字符
    max_count = 0
    most_frequent = ''
    for char, count in s_dict.items():
        if count > max_count:
            max_count = count
            most_frequent = char
    return most_frequent, max_count

s = 'abcaadabscura'
print(count(s))

"""
知识点：
1.集合的创建和运用
2.集合的键值对应
3.集合的方法使用(items...)
"""
```

