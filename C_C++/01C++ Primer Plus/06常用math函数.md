# 常用math函数

## 汇总表

| 类型             | 示例                        | 含义                               |
| ---------------- | --------------------------- | ---------------------------------- |
| 绝对值函数       | `fabs(x)`                   | $fabs(x) = |x|$                    |
| 向上取整函数     | `ceil(x)`                   | $ceil(x) = \lceil x \rceil$        |
| 向下取整函数     | `floor(x)`                  | $floor(x) = \lfloor x \rfloor$     |
| 四舍五入取整函数 | `round(x)`                  | $round(x) = \lfloor x+0.5 \rfloor$ |
| 幂函数           | `pow(a, x)`                 | $pow(a, x) = a^x$                  |
| 平方根函数       | `sqrt(x)`                   | $sqrt(x) = \sqrt {x}$              |
| 对数函数         | `log(x)`                    | $log(x) = ln(x)$                   |
| 三角函数         | `sin(x), cos(x), tan(x)`    |                                    |
| 反三角函数       | `asin(x), acos(x), atan(x)` |                                    |

## 测试代码

`test_math.h文件`

```c++
//
// Created by Administrator on 2022/2/10/010.
//
#include "iostream"
#include "cmath"

using namespace std;


#ifndef ALOGRITHMNOTEBOOK_TEST_MATH_H
#define ALOGRITHMNOTEBOOK_TEST_MATH_H

void test_math_function() {
    double x1, x2;
    cout << "请输入x1和x2的值进行测试" << endl;
    cin >> x1 >> x2;
    cout << "x1:" << x1 << endl
         << "fabs(x1):" << fabs(x1) << endl
         << "ceil(x1):" << ceil(x1) << endl
         << "floor(x1):" << floor(x1) << endl
         << "round(x1)" << round(x1) << endl
         << "pow(x1, x2):" << pow(x1, x2) << endl
         << "sqrt(x1):" << sqrt(x1) << endl
         << "log(x1):" << log(x1) << endl
         << "sin(x1):" << sin(x1) << endl
         << "cos(x1):" << cos(x1) << endl
         << "tan(x1):" << tan(x1) << endl
         << "asin(x1):" << asin(x1) << endl
         << "acos(x1):" << acos(x1) << endl
         << "atan(x1):" << atan(x1) << endl;
}

#endif //ALOGRITHMNOTEBOOK_TEST_MATH_H

```

