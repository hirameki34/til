# 使用 PyTorch 自动微分

使用 PyTorch，对函数 $y=2x^Tx$ 积分。

``` python
import torch

x = torch.arange(4.0) # tensor([0., 1., 2., 3.])
```

在计算梯度之前，需要一个位置存储梯度。
使用 `requires_grad_(True)`
或在创建 Tensor 时指定 `requires_grad=True`，
表示这个 Tensor 要被用于计算梯度。

``` python
x.requires_grad_(True)  # 等价于x=torch.arange(4.0,requires_grad=True)
print(x.grad)  # None
```

计算 y

``` python
y = 2 * torch.dot(x, x) # tensor(28., grad_fn=<MulBackward0>)
```

接下来，通过调用反向传播函数来自动计算y关于x每个分量的梯度。

```python
y.backward()
print(x.grad) # tensor([ 0.,  4.,  8., 12.])
```

计算 x 的另一个函数，$y = \Sigma x_i$
在默认情况下，PyTorch会累积梯度，我们需要清除之前的值。

``` python
x.grad.zero_()
y = x.sum()
y.backward()
print(x.grad) # tensor([1., 1., 1., 1.])
```

## 分离计算

对于 $y = x \cdot x$ 和 $z = y \cdot x$ 两个函数。

有时，我们希望将某些计算移动到记录的计算图之外。
并且只考虑到 $x$ 在 $y$ 被计算后发挥的作用。

这里可以分离 $y$ 来返回一个新变量 $u$，该变量与 $y$ 具有相同的值，
但丢弃计算图中如何计算 $y$ 的信息。
换句话说，梯度不会向后流经 $u$ 到 $x$。
反向传播函数计算 $z=u \cdot x$ 关于 $x$ 的偏导数，
同时将 $u$ 作为常数处理。

```python
x.grad.zero_()
y = x * x
u = y.detach()
z = u * x

z.sum().backward()
print(x.grad == u) # tensor([True, True, True, True])
```

## 自定义函数的自动微分

即使构建函数的计算图需要通过Python控制流（例如，条件、循环或任意函数调用），
我们仍然可以计算得到的变量的梯度。

``` python
def f(a):
    b = a * 2
    while b.norm() < 1000:
        b = b * 2
    if b.sum() > 0:
        c = b
    else:
        c = 100 * b
    return c
```

``` python
a = torch.randn(size=(), requires_grad=True)
d = f(a)
d.backward()
```

函数 `f()` 在其输入a中是分段线性的，可以用 $d / a$ 验证梯度是否正确。

``` python
a.grad == d / a # tensor(True)
```

---

**Reference**: [2.5. 自动微分 — 动手学深度学习 2.0.0 documentation](https://zh.d2l.ai/chapter_preliminaries/autograd.html)
