---
title: 类
published: 2023-12-06
pinned: false
tags: [Python]
category: 学习项目
draft: false
---
* **类**: 用来描述具有相同的属性和方法的对象的集合。 
* **方法**:类中定义的函数。
* **对象**: 通过类定义的数据结构实例。
* **属性** :私有属性即类**内**的变量
### 默认方法` __init__()`

```python
class Complex: 
	def __init__(self, realpart, imagpart): 
		self.r = realpart 
		self.i = imagpart 
x = Complex(3.0, -4.5) 
print(x.r, x.i) # 输出结果：3.0 -4.5
```

`__init__()`相当于向私有变量赋值的窗口
### `self`

* self代表类的实例，而非类

类的方法与普通的函数只有一个特别的区别——它们必须有一个额外的**第一个参数名称**, 按照惯例它的名称是 self。

### 类的方法
在类的内部，使用 `def`关键字来定义一个方法,与一般函数定义不同,类方法必须包含参数 self, 且为第一个参数，self 代表的是类的实例。

```python 
#!/usr/bin/python3
 
#类定义
class people:
    #定义基本属性
    name = ''
    age = 0
    #定义私有属性,私有属性在类外部无法直接进行访问
    __weight = 0
    #定义构造方法
    def __init__(self,n,a,w):
        self.name = n
        self.age = a
        self.__weight = w
    def speak(self):
        print("%s 说: 我 %d 岁。" %(self.name,self.age))
 
# 实例化类
p = people('runoob',10,30)
p.speak()
```

### 继承
```python
class father:  
    first =11  
    second = 22  
    def __init__(self, first, second):  
        self.first = first  
        self.second = second  
  
  
class son(father):  
    def __init__(self, first, second, third, fourth):  
        father.__init__(self, first, second)  
        self.third = third  
        self.fourth = fourth  
  
    def speak(self):  
        print(self.first, self.second, self.third, self.fourth)  
  
  
a = son(1, 2, 3, 40)  
a.speak()
# 1 2 3 40
```
相当于`son`从`father`处继承了两个变量名,但值为传入的值
