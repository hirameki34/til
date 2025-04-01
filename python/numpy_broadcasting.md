# Numpy 的广播机制

关于多维数组（张量）的数学运算建立在二者形状相同的前提下。
通过对每个对应位置的标量进行计算，得到最终结果。

``` python
import numpy as np
a = np.array([1.0, 2.0, 3.0])
b = np.array([2.0, 2.0, 2.0])
a * b
```

对于形状不同的张量，Numpy 可以通过 *广播* 进行处理。
当两个数组满足某些规则时，较小的数组可以 “广播” 到较大的数组上。

```python
import numpy as np
a = np.array([1.0, 2.0, 3.0])
b = 2.0
a * b
```

![broadcast](https://numpy.org/doc/stable/_images/broadcasting_1.png)

当操作两个数组时，NumPy 会逐元素比较它们的形状。它从最右侧的维度开始，
向左逐个维度进行比较。二者形状兼容需要满足：

- 当两个维度相等，或者
- 一个维度为 1

简单来说，**某个维度为 1 的数组兼容其他数组**，
可以通过复制的方式填补这个维度，
进而和其他数组进行运算。

以下是一些可以广播的例子：

``` plaintext
A      (2d array):  5 x 4
B      (1d array):      1
Result (2d array):  5 x 4

A      (2d array):  5 x 4
B      (1d array):      4
Result (2d array):  5 x 4

A      (3d array):  15 x 3 x 5
B      (3d array):  15 x 1 x 5
Result (3d array):  15 x 3 x 5

A      (3d array):  15 x 3 x 5
B      (2d array):       3 x 5
Result (3d array):  15 x 3 x 5

A      (3d array):  15 x 3 x 5
B      (2d array):       3 x 1
Result (3d array):  15 x 3 x 5
```

---

**Reference**: [Broadcasting — NumPy v2.2 Manual](https://numpy.org/doc/stable/user/basics.broadcasting.html)
