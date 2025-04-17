# 权重衰减

> 浏览[英文版的 Dive Into Deep Learning](https://d2l.ai/index.html) 时，
> 发现已经比 [中文版](https://zh.d2l.ai/) 更新了一个大版本。
> 出现了不少新内容。
>
> 新版的组织方式更加现代化，符合最佳的工程实践。也包含了更多现代的机器学习技术，比如 权重衰减。

## 背景：模型的过拟合

过拟合是指模型对训练数据拟合得比底层分布更紧密的情况。

在标准的机器学习设置中，我们假设训练数据和测试数据是独立地从相同的分布中抽取的（独立同分布假设）。
独立同分布是很强的假设，当训练数据和测试数据不满足这个假设，就会出现过拟合的问题。

数学上，可以定义两个误差：训练误差 $R_{emp}$ 和 泛化误差 $R$

训练误差是一个统计量，是每个样本的估计值和实际值误差的和。

$$
R_{emp}[\mathbf{X},\mathbf{y}, f] = \frac{1}{n}\sum_{i=1}^{n}l(\mathbf{x}^{(i)},y,f(\mathbf{x}^{(i)}))
$$

泛化误差是一个期望，表示在样本遵循的分布下，误差的期望。

$$
R[p, f] = E_{(\mathbf{x},y)\sim P}[l(\mathbf{x},y,f(\mathbf{x}))] = \iint l(\mathbf{x},y,f(\mathbf{x}))p(\mathbf{x},y) {} d\mathbf{x}dy
$$

我们无法知道密度函数 $p(\mathbf{x},y)$，因此，泛化误差无法精确计算。

通常，我们用验证集上的误差（验证误差）表示泛化能力。
当比较训练误差和验证误差时，如果训练误差显著低于验证误差，则说明出现了严重的过拟合。

![fitting](https://d2l.ai/_images/capacity-vs-error.svg)

## 范数与权重衰减

应对权重衰减的技术成为 正则化（Regularization）技术。

权重衰减（Weight Decay）就是一种正则化技术。
在深度学习领域之外，权重衰减通常被称为 $\mathcal{l}_2$ 正则化。

权重衰减将损失函数添加了一个惩罚项，惩罚权重向量的长度：

$$
L(\mathbf{w},b)+\frac{\lambda}{2}\|\mathbf{w}\|^2
$$

$\mathcal{l}_2$ 正则化回归的随机梯度下降更新如下：

$$
\mathbf{w} \leftarrow (1 - \eta \lambda) \mathbf{w} - \frac{\eta}{|\mathcal{B}|}\sum_{i \in \mathcal{B}}\mathbf{x}^{(i)}(\mathbf{w}^\top \mathbf{x}^{(i)} + b - y^{(i)})
$$

对比没有权重衰减的更新：

$$
\mathbf{w} \leftarrow \mathbf{w} - \frac{\eta}{|\mathcal{B}|}\sum_{i \in \mathcal{B}}\mathbf{x}^{(i)}(\mathbf{w}^\top \mathbf{x}^{(i)} + b - y^{(i)})
$$

权重衰减从中减去了 $\eta \lambda \mathbf{w}$，较小的 $\lambda$ 表示对权重限制更小。

---

**Reference**:

[3.6. Generalization — Dive into Deep Learning 1.0.3 documentation](https://d2l.ai/chapter_linear-regression/generalization.html)

[3.7. Weight Decay — Dive into Deep Learning 1.0.3 documentation](https://d2l.ai/chapter_linear-regression/weight-decay.html)
