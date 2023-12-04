# 自定义 `matplotlib.pyplot` 绘图颜色

[Palettable](https://pypi.org/project/palettable/) 是一个 Python 库，提供了多种配色方案。

## 安装

``` bash
pip install palettable
```

## 使用

### 设置 `Color Cycle`

```python
colors = palettable.colorbrewer.qualitative.Dark2_8
ax.set_prop_cycle('color', colors.mpl_colors)
```

### 设置 `Colormap`

```python
ax.imshow(data, cmap=colors.mpl_colormap)
```

### 设置 `Discrete Colormap`

```python
cmap = ListedColormap(colors.mpl_colors)
```

## 查看配色

```python
colors.show_discrete_image()
colors.show_continuous_image()
```

## 常用配色

- 对比色：`colorbrewer.qualitative.Paired_n.mpl_colormap`

Refrence: [Palettable |
Color palettes for Python](https://jiffyclub.github.io/palettable/)
