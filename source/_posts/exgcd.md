---
title: 扩展欧几里得算法
date: 2021-01-26 12:49:27
tags: Alogrithm
mathjax: true
---

### 扩展欧几里得算法

在求最大公约数时，基于一个基本的事实$(a, b) = (b, a - \lfloor \frac{a}{b}\rfloor b) = (b, a\ mod \ b)$，直到$b = 0$，此时$(a, 0) = a$。这种求最大公约数的算法叫做辗转相除法，又叫做欧几里得算法。

```c++
int gcd(int a, int b) {
	while (b != 0) {
		a %= b;
		swap(a, b);
	}
	return a;
}
```

**裴蜀定理**：假设$a, b$是不全为0的整数，则存在整数$x, y$使得$ax + by = gcd(a, b)$。

简单证明：
$$
gcd(a, b) = gcd(b, a\ mod\ b) = gcd(b, a - kb) = gcd(r_1, r_2) = \dots = gcd(r_n, 0)
$$
其中$r_1, r_2 \dots r_n$都可以写成$x_ia + y_ib$的形式，因此存在$ax + by = gcd(a, b)$

扩展欧几里得算法在求解最大公约数的同时，计算了一组适于裴蜀定理的系数。
递推关系的推导，$(a, b) = (b, a\ mod\ b) = (b, a - \lfloor \frac{a}{b} \rfloor b)$，因此存在四个整数$x, y, x', y'$使得
$$
ax + by = bx' + (a - \lfloor \frac{a}{b} \rfloor b)y' \\\\
a(x - y') + b(y - (x' - \lfloor \frac{a}{b} \rfloor)y') = 0 \\\\
x = y', y = x' - \lfloor \frac{a}{b} \rfloor y'
$$

```c++
ex_gcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    int d = exgcd(b, a % b, x, y);
    int tmp = x;
    x = y;
    y = tmp - a / b * y;
    return d;
}
```

上述算法可以改写为迭代的形式，递推公式
$$
\left\{ \begin{array}{rcl}
{\rm{x}} = y' \\\\
{y = x' - \lfloor \frac{a}{b} \rfloor y'}
\end{array} \right.
$$
将其写为矩阵的形式

$$
\left( \begin{array}{rcl}
x \\\\
y
\end{array} \right) = \left( \begin{array}{rcl}
0&1 \\\\
1&{ -\lfloor \frac{a}{b} \rfloor}
\end{array} \right)\left( \begin{array}{rcl}
{x'} \\\\
{y'}
\end{array} \right)
$$

+ 初始矩阵为单位矩阵
+ $
  \left( \begin{array}{rcl}
  x&m\\\\
  y&n
  \end{array} \right) = \left( \begin{array}{rcl}
  x&m\\\\
  y&n
  \end{array} \right)\left( \begin{array}{rcl}
  0&1\\\\
  1&{ - d}
  \end{array} \right) = \left( \begin{array}{rcl}
  m&{x - dm}\\\\
  n&{y - dn}
  \end{array} \right)$
+ 当b=0时，计算结束，此时x, y为满足裴蜀定理的系数，最大公约数为a。