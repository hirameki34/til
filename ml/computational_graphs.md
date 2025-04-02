# 神经网络中的计算图

计算图通过图的数据结构描述了一个复合数学运算。
是神经网络计算的基石。
常见的深度学习框架包括 TensorFlow、PyTorch、MXNet 等，
在内部使用计算图来表示和执行各种计算操作。

在计算图中，**节点表示值或运算**，**边表示数据的流动方向**。
对于表达式 $g=(x+y)∗z$，对应这样的计算图：

![cg](https://www.tutorialspoint.com/python_deep_learning/images/computational_graph_equation2.jpg)

详细来说，计算图通常 [^1] 是一个有向无环图 (DAG)。其中包含这些类型的节点：

1. 入度为 0 的节点：表示输入，是一个数值。
2. 出度为 0 的节点：表示输出，也是一个数值。
3. 其他中间节点：表示某种运算。

[^1]: 循环神经网络 (GNN) 在概念上包含环，但可以通过某些操作展开，形成 DAG。

使用计算图有两种模式：前向传播和反向传播。

**前向传播** (Forward Pass) 用于计算表达式的值。
前线传播从输入开始，按照拓扑排序的顺序执行，得到输出。

![fp](https://www.tutorialspoint.com/python_deep_learning/images/forward_pass_equation.jpg)

**反向传播** (Backward Pass / Backpropagation)
的目的是计算每个输入相对于最终输出的梯度。
从输出开始，梯度通过链式法则计算，逐步计算到输入。
每个操作节点都知道如何计算自己的局部导数，通过组合这些局部导数计算完整的梯度。

---

**Read More**： [使用 PyTorch 自动微分](/python/pytorch_automatic_differentiation.md)

**Reference**:

[Computational graphs and gradient flows — Simple English Machine Learning documentation](https://simple-english-machine-learning.readthedocs.io/en/latest/neural-networks/computational-graphs.html)

[Computational Graphs in Deep Learning](https://www.tutorialspoint.com/python_deep_learning/python_deep_learning_computational_graphs.htm)

[A Gentle Introduction to Tensors and Computational Graphs in Neural Networks | by Felipe Fernandez | Medium](https://medium.com/@ofelipefernandez/gentle-introduction-to-tensors-and-computational-graphs-in-neural-networks-929b5b0ddc5f)
