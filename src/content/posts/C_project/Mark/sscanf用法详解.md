---
title: sscanf用法详解
published: 2022-07-26
pinned: false
description: sscanf用法详解
tags: [C语言,项目]
category: 学习项目
draft: false
---

## 说明

**sscanf**读取格式化的字符串中的数据。

**swscanf**是 sscanf 的宽字符版本；swscanf 的参数是宽字符串。

 sscanf不处理多字节的十六进制字符。 swscanf不处理 Unicode 全角十六进制或"兼容性区"字符。 除此以外，swscanf 和 sscanf 的行为完全相同。

## 函数语法

`int sscanf(const char *buffer,  const char *format, [argument] ... );`

## 参数

**buffer** 存储的数据

**format** 窗体控件字符串。 有关详细信息，请参阅"格式规范"。

**argument** 可选自变量

**locale** 要使用的区域设置

## 头文件

|    例程    | 必须的头文件 |
| :--------: | :----------: |
|   sscanf   |  < stdio.h>  |
|_ sscanf_l  |  < stdio.h>  |
|swscanf   |  < stdio.h>  |
|_ swscanf_l |  <wchar.h>   |

## 使用

sscanf与scanf类似，都是用于输入的，只是后者以键盘(stdin)为输入源，前者以固定字符串为输入源。

第二个参数可以是一个或多个 `{%[*] [width] [{h | I | I64 | L}]type | ' ' | '\t' | '\n' | 非%符号}`

注：

1. \*亦可用于格式中, (即 %\*d 和 %\*s) 加了 星号 (\*) 表示跳过此数据**不读入**. (也就是不把此数据读入参数中)
2. {a|b|c}表示a,b,c中选一，[d]表示可以有d也可以没有d。
3. width表示读取宽度。
4. {h | l | I64 | L}: 参数的size，通常h表示单 字节size，I表示2字节 size，L表示4字节size(double例外)，l64表示8字节size。
5. type ：这就很多了，就是%s,%d之类。
6. 特别的：%*[width] [{h | l | I64 | L}]type 表示满足该条件的被过滤掉，不会向目标参数中写入值。失败返回0 ，否则返回格式化的参数个数

## 返回值

每个函数都将返回成功转换并分配的字段数；返回值不包括已读取但未分配的字段。 返回值为 0 表示没有分配任何字段。 返回值是EOF是否有错误或如果在第一次转换之前到达字符串结尾。

成功则返回参数数目，失败则返回 -1，错误原因存于errno中。

## 使用实例

### 1.%s以空格作为分隔符

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[10] = "12345 678";
	sscanf(c, "%s", ans);
	printf("%s", ans);
	return 0;
}
```

**输出结果：12345**

### 2.获取指定长度的字符串

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[10] = "12345678";
	sscanf(c, "%4s", ans);
	printf("%s", ans);
	return 0;
}
```

**输出结果：1234**

### 3.获取以指定字符为终止的字符串

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[10] = "12345678";
	sscanf(c, "%[^6]s", ans);	//以6为终止符
	printf("%s", ans);
	return 0;
}
```

**输出结果：12345**

### 4.获取仅包含指定字符集的字符串

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[15] = "0128aDacEdfQ";
	sscanf(c, "%[0-9a-z]", ans);
	printf("%s", ans);
	return 0;
}
```

**输出结果：0128a**

注意：匹配到第一个不在范围内的为止，后面的 *ac* 和 *df* 不会匹配到

### 5.获取到指定字符集为止的字符串

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[15] = "0128aDacEdfQ";
	sscanf(c, "%[^E-Z]", ans);
	printf("%s", ans);
	return 0;
}
```

**输出结果：0128aDac**

### 6.给定一个字符串， 获取 / 和 @ 之间的字符串

先将 '/' 前的内容过滤掉，匹配 '/'，再将非 '@' 的一串内容送到ans中。

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[15] = "01/28aDacE@dfQ";
	sscanf(c, "%*[^/]/%[^@]", ans);
	printf("%s", ans);
	return 0;
}
```

**输出结果：28aDac**

注意：这样的写法是错误的 `sscanf(c, "%*s/%[^@]", ans);`

### 7.给定一个字符串，含一个空格，取出空格后面的字符串

%s的读取是以空白符（空格或者tab）结束的。

```cpp
#include <iostream>
using namespace std;
int main() {
	char ans[10], c[15] = "hello, world";	//逗号后面有一个空格
	sscanf(c, "%*s%s", ans);
	printf("%s", ans);
	return 0;
}
```

**输出结果：world**

### 8.用于辅助判断输入格式是否正确

如[洛谷P7911 网络连接](https://www.luogu.com.cn/problem/P7911)一题中，要求判断输入的地址串是否格式正确（形如a.b.c.d:e），可以用sscanf辅助判断。

```cpp
#include <iostream>
using namespace std;
int main() {
	char ch[20] = "192.168.1.1:8080";
	int a, b, c, d, e;
	int x = sscanf(ch, "%d.%d.%d.%d:%d", &a, &b, &c, &d, &e);
	printf("%d %d %d %d %d x = %d", a, b, c, d, e, x);
	return 0;
}
```

**输出结果：192 168 1 1 8080 x = 5**

若其返回值x不为5，则一定错误。如字符串为 "192.168.1:1:8080" ，输出为：192 168 1 0 0 x = 3

但是若返回值为5，**不一定**格式正确。如字符串为 "192.168.1.1:8080:12" ，输出为：192 168 1 1 8080 x = 5，其与本样例输出相同，但显然不是正确的格式。

## 小结

sscanf的功能很类似于**正则表达式**，但却没有正则表达式强大，所以如果**对于比较复杂的字符串处理，建议使用 正则表达式。**