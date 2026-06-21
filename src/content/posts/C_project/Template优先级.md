---
title: Template优先级
published: 2022-08-03
pinned: false
description: Template优先级
tags: [C/Cpp,语法]
category: 学习项目
draft: false
---

### ① 通用模板 (Primary Template)
```cpp
template<typename T> 
T add(T x, T y) { ... }
```
*   **身份**：这是“模具”本身，也就是默认的实现方案。
*   **作用**：它是一个蓝图。当你传入 `int`、`float` 或其他支持 `+` 运算的类型时，编译器会根据这个蓝图自动生成代码。
*   **地位**：它是兜底的选项。如果没有更具体的匹配，就用它。

#### ② 模板显式特化 (Explicit Specialization)
```cpp
template <> 
double add(double x, double y) { ... }
```
*   **身份**：这是专门为 `double` 类型定制的“特供版”。
*   **语法特征**：注意 `template <>` 这里的空尖括号，以及函数名后明确的 `<double>`（虽然这里省略了，但语义上是针对特定类型的）。
*   **作用**：你告诉编译器：“当 `T` 是 `double` 时，不要用上面的通用蓝图，请直接使用我写的这个专门版本。”
*   **地位**：它是模板家族的一员，但是是针对特定类型的“定制款”。

#### ③ 普通函数重载 (Non-template Overload)
```cpp
double add(double x, double y) { ... }
```
*   **身份**：这是一个完全独立的、普通的 C++ 函数。
*   **语法特征**：没有 `template` 关键字，就是一个标准的函数定义。
*   **作用**：它就只是一个接受两个 `double` 参数的函数。
*   **地位**：它不属于模板家族，它是一个“局外人”，但在重载决议中有极高的优先级。

```cpp
#include <iostream>

  

template<typename T> T add(T x, T y) {

    std::cout << "this is the template version of add" << std::endl;

    std::cout << "The size of x is: " << sizeof(x) << " bytes" << std::endl;

    return x + y;

}

template <> double add(double x, double y) {

    std::cout << "this is the template specialization of add for double" << std::endl;

    std::cout << "The size of x is: " << sizeof(x) << " bytes" << std::endl;

    return x + y;

}

// double add(double x, double y) {

//     std::cout << "this is the non-template version of add" << std::endl;

//     std::cout << "The size of x is: " << sizeof(x) << " bytes" << std::endl;

//     return x + y;

// }

int main() {

    std::cout << add((float)3.0, (float)4.2) << std::endl;

    return 0;

}
```