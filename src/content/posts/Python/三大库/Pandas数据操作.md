---
title: Pandas数据操作
published: 2024-02-07
pinned: false
tags: [Python]
category: 学习项目
draft: false
---
### 重新索引
#### 改变索引
```python
import pandas as pd  
  
table = {'环比': [101.5, 101.2, 101.3, 102.0, 100.1],  
         '同比': [120.7, 127.3, 119.4, 140.9, 101.4],  
         '定基': [121.4, 127.8, 120.0, 145.5, 101.6]}  
a = pd.DataFrame(table, index=['北京', '上海', '广州', '深圳', '沈阳'])  
a = a.reindex(columns=['定基','环比','同比'])  
print(a)
```
则表头(第一行)由[原来的](Pandas数据类型.md#从字典创建DataFrame)`环比 同比 定基`变为`定基 环比 同比`

#### 重排索引
```python
import pandas as pd  
  
table = {'环比': [101.5, 101.2, 101.3, 102.0, 100.1],  
         '同比': [120.7, 127.3, 119.4, 140.9, 101.4],  
         '定基': [121.4, 127.8, 120.0, 145.5, 101.6]}  
a = pd.DataFrame(table, index=['北京', '上海', '广州', '深圳', '沈阳'])  
a = a.reindex(index=['沈阳','深圳','广州','上海','北京'])  
print(a)
```
则索引(第一列)由[原来的](Pandas数据类型.md#从字典创建DataFrame)`北京 上海 广州 深圳 沈阳`变为`沈阳 深圳 广州 上海 北京`

#### etc.
参照上例
##### `.reindx(index=None,Column=None,....)
| 参数 | 说明 |
| :--: | :--: |
| index coloums | 定义行列索引 |
| fill_value(column,value) | 在column列填充value |
| method | 填充方法，fill当前值向前填充，bfill向后填充 |
| limit | 最大填充量 |
| copy | 默认True,生成新的对象，False时， 新旧相等不复制 |

| 方法 | 说明 |
| :--: | :--: |
| .append(idx) | 连接另一个Index对象，产生新的Index对象 |
| .diff(idx) | 计算差集，产生新的Index对象 |
| .intersection(idx) | 计算交集 |
| .union(idx) | 计算并集 |
| .delete(loc) | 删除loc位置处的元素 |
| .insert(loc,e) | 在loc位置增加一个元素e |
| .drop(索引) | 删除索引及其对应的值 |
### 算数运算
* 算术运算根据行列索引，补齐后运算，即相同索引才进行运算
* 运算默认产生浮点数
* 补齐时缺项填充NaN 
* 维度不同时采用广播运算
* 采用+- * /符号进行的二元运算产生新的对象

| 方法 | 说明 |
| :--: | :--: |
| .add(b,dvalue) | 将DataFram与b相加，对应位置缺少时用value补齐 |
| .sub(b,dvalue) | 将DataFram与b相减，对应位置缺少时用value补齐 |
| .mul(b,dvalue) | 将DataFram与b相乘，对应位置缺少时用value补齐 |
| .div(b,dvalue) | 将DataFram与b相除，对应位置缺少时用value补齐 |
### 排序
#### `.sort_index(axis=0,ascending=True)`
* axis指定排序依据的轴
* ascending指定排序方式，默认升序
* NaN统一放末尾
#### `[Series].sort_values(axis=0,ascending=True)`或`[DataFrame].sort_value(by,axis=0,ascending=True)`
* axis指定排序依据的轴
* ascending指定排序方式，默认升序
* 排DataFrame时需要用过参数`by`指定axis轴上某个索引或索引列表
*  NaN统一放末尾
### 统计分析
| 方法 | 说明 |
| :--: | :--: |
| .sum() | 计算数据的总和，按0轴计算，下同 |
| .count() | 非NaN值的数量 |
| .mean() .median() | 计算数据的算术平均值、算术中位数 |
| .var() .std() | 计算数据的方差、标准差 |
| .min() .max() | 计算数据的最小值、最大值 |
| .describe | 返回 针对0轴（各列）的各种统计结果，是一个Series |
| .cumsum() | 依次输出前1,2,3....n的和 |
| .cumprod() | 依次输出前1,2,3....n的积 |
| .cummax() | 依次输出前1,2,3....n的最大值 |
| .cummin() | 依次输出前1,2,3....n的最小值 |
| .rolling(w).sum() | 依次计算相邻w个元素的和 |
| .rolling(w).mean() | 依次计算相邻w个元素的算术平均值 |
| .rolling(w).var() | 依次计算相邻w个元素的方差 |
| .rolling(w).std | 依次计算相邻w个元素的标准差 |
| .rolling(w).min .max | 依次计算相邻w个元素的最小值或最大值 |
*上述方法适用于Series 和 DataFrame*

| 方法 | 说明 |
| :--: | :--: |
| .argmin()  .argmax() | 返回最小值 最大值 的 自动索引 |
| .idxmin()  .idxmax() | 返回最小值 最大值 的 自定义索引 |
*上述方法只适用于Series*

### 相关分析
| 方法 | 说明 |
| :--: | :--: |
| .cov() | 计算协方差矩阵 |
| .corr() | 计算相关系数矩阵 |
*上述方法适用于Series 和 DataFrame*