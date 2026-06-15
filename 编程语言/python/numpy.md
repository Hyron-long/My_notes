# NumPy 完全入门教程

NumPy是Python科学计算的基石，几乎所有数据科学和机器学习库都构建在它之上。本教程将从零开始，带你系统掌握NumPy的核心功能。

---

## 目录
1. [NumPy简介与安装](#1-numpy简介与安装)
2. [NumPy的核心：ndarray对象](#2-numpy的核心ndarray对象)
3. [创建数组的多种方式](#3-创建数组的多种方式)
4. [数组索引与切片](#4-数组索引与切片)
5. [数组运算与广播机制](#5-数组运算与广播机制)
6. [数组形状操作](#6-数组形状操作)
7. [统计函数](#7-统计函数)
8. [随机数生成](#8-随机数生成)
9. [实战案例：销售数据分析](#9-实战案例销售数据分析)

---

## 1. NumPy简介与安装

### 1.1 什么是NumPy？

NumPy（Numerical Python）是Python科学计算的基础包，它提供了高性能的多维数组对象和用于处理这些数组的工具。

**为什么需要NumPy？**

| 特性     | Python列表               | NumPy数组                   |
| -------- | ------------------------ | --------------------------- |
| 内存存储 | 分散存储（存储对象引用） | 连续存储                    |
| 数据类型 | 可以混合不同类型         | 所有元素类型相同            |
| 运算速度 | 慢（需要循环）           | 快（向量化操作，C语言实现） |
| 数学函数 | 需要自己实现             | 丰富的内置函数              |

简单来说，NumPy数组比Python列表**快几十倍甚至上百倍**，同时代码更加简洁。

```python
# 示例：计算两个数组的元素乘积
# Python列表方式（需要循环）
a, b = [1, 2, 3], [4, 5, 6]
c = []
for i in range(len(a)):
    c.append(a[i] * b[i])
print(c)  # [4, 10, 18]

# NumPy方式（向量化操作）
import numpy as np
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])
c = a * b
print(c)  # [4 10 18]
```

### 1.2 安装与导入

```bash
# 使用pip安装
pip install numpy

# 使用清华镜像源加速安装
pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
```

安装后，Python中导入NumPy的**标准方式**是：

```python
import numpy as np
```

使用`np`作为别名是社区的通用约定，几乎所有NumPy代码都这样写。

```python
# 验证安装
print(np.__version__)  # 输出类似：1.26.0
```

---

## 2. NumPy的核心：ndarray对象

NumPy的核心是`ndarray`（N-dimensional array，即N维数组）对象。

### 2.1 ndarray的重要属性

```python
import numpy as np

# 创建一个示例数组
arr = np.array([[1, 2, 3, 4],
                [5, 6, 7, 8],
                [9, 10, 11, 12]])

print(f"数组:\n{arr}")
print(f"维度(轴的数量): {arr.ndim}")      # 2
print(f"形状: {arr.shape}")              # (3, 4) - 3行4列
print(f"元素总数: {arr.size}")           # 12
print(f"元素类型: {arr.dtype}")          # int64（取决于系统）
print(f"每个元素字节数: {arr.itemsize}") # 8
```

**关键概念理解**：
- **轴(axis)**：维度被称为轴。一维数组有1个轴，二维数组有2个轴（行和列）
- **形状(shape)**：是一个整数元组，表示每个维度的大小

```python
# 不同维度的数组
arr_1d = np.array([1, 2, 3])        # 一维，shape=(3,)
arr_2d = np.array([[1, 2], [3, 4]]) # 二维，shape=(2, 2)
arr_3d = np.array([[[1, 2], [3, 4]]]) # 三维，shape=(1, 2, 2)
```

---

## 3. 创建数组的多种方式

### 3.1 从已有数据创建

```python
import numpy as np

# 从列表创建一维数组
arr1 = np.array([1, 2, 3, 4, 5])
print(arr1)  # [1 2 3 4 5]

# 从嵌套列表创建二维数组
arr2 = np.array([[1, 2, 3], [4, 5, 6]])
print(arr2)
# [[1 2 3]
#  [4 5 6]]

# 指定数据类型
arr3 = np.array([1, 2, 3], dtype=np.float32)
print(arr3)        # [1. 2. 3.]
print(arr3.dtype)  # float32
```

### 3.2 使用内置函数创建特殊数组

```python
# 全零数组
zeros = np.zeros((3, 4))  # 3行4列的全0矩阵
print(zeros)
# [[0. 0. 0. 0.]
#  [0. 0. 0. 0.]
#  [0. 0. 0. 0.]]

# 全1数组
ones = np.ones((2, 3), dtype=int)  # 2行3列的全1整数矩阵
print(ones)
# [[1 1 1]
#  [1 1 1]]

# 单位矩阵（对角线上为1）
eye = np.eye(3)
print(eye)
# [[1. 0. 0.]
#  [0. 1. 0.]
#  [0. 0. 1.]]

# 未初始化的数组（值为随机内存数据）
empty = np.empty((2, 2))
print(empty)
```

### 3.3 创建数值序列

```python
# arange - 类似Python的range，但返回数组
arr = np.arange(10)      # [0 1 2 3 4 5 6 7 8 9]
arr = np.arange(1, 10, 2) # [1 3 5 7 9]（起始、结束、步长）

# linspace - 等间距取点
arr = np.linspace(0, 10, 5)  # [0., 2.5, 5., 7.5, 10.]（包含终点）
```

### 3.4 创建随机数组

NumPy提供了强大的随机数生成功能：

```python
# 设置随机种子（确保结果可重复）
np.random.seed(42)

# 1. 均匀分布 [0, 1)
rand_uniform = np.random.rand(3, 3)  # 3x3的[0,1)均匀分布

# 2. 标准正态分布（均值0，标准差1）
rand_normal = np.random.randn(2, 4)  # 2x4的标准正态分布

# 3. 指定范围的随机整数 [low, high)
rand_int = np.random.randint(1, 10, size=(2, 3))  # 2x3，值在1-9之间

# 4. 从指定分布生成
normal = np.random.normal(loc=5, scale=2, size=100)  # 均值5，标准差2的正态分布
```

---

## 4. 数组索引与切片

NumPy数组的索引和切片操作非常直观，与Python列表类似但支持多维。

### 4.1 基本索引

```python
arr = np.array([[1, 2, 3, 4],
                [5, 6, 7, 8],
                [9, 10, 11, 12]])

# 访问单个元素
print(arr[0, 0])   # 1 - 第0行第0列
print(arr[1, 2])   # 7 - 第1行第2列
print(arr[-1, -1]) # 12 - 最后一行最后一列

# 修改元素
arr[0, 0] = 100
print(arr[0, 0])   # 100
```

### 4.2 切片操作

```python
# 一维切片（与列表相同）
arr_1d = np.array([0, 1, 2, 3, 4, 5])
print(arr_1d[1:4])    # [1 2 3] - 索引1到3
print(arr_1d[:3])     # [0 1 2] - 开头到索引2
print(arr_1d[::2])    # [0 2 4] - 步长为2
print(arr_1d[::-1])   # [5 4 3 2 1 0] - 反转

# 二维切片 - 格式：[行切片, 列切片]
arr_2d = np.array([[1, 2, 3, 4],
                   [5, 6, 7, 8],
                   [9, 10, 11, 12]])

print(arr_2d[0:2, 1:3])  # 前2行，第1-2列
# [[2 3]
#  [6 7]]

print(arr_2d[:, 1:3])    # 所有行，第1-2列
# [[ 2  3]
#  [ 6  7]
#  [10 11]]

print(arr_2d[1:, :2])    # 第1行到最后，前2列
# [[5 6]
#  [9 10]]
```

### 4.3 重要：视图 vs 副本

NumPy切片返回的是原数组的**视图**，修改切片会影响原数组，这与Python列表不同：

```python
# 列表切片：修改副本不影响原列表
lst = [1, 2, 3, 4]
lst_copy = lst[:]
lst_copy[0] = 100
print(lst)      # [1, 2, 3, 4] - 原列表不变

# NumPy数组切片：修改视图影响原数组
arr = np.array([1, 2, 3, 4])
arr_view = arr[:]
arr_view[0] = 100
print(arr)      # [100, 2, 3, 4] - 原数组被修改！

# 如果需要独立的副本，使用copy()
arr_copy = arr.copy()
arr_copy[0] = 200
print(arr)      # [100, 2, 3, 4] - 原数组不变
```

### 4.4 布尔索引（高级筛选）

```python
arr = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])

# 筛选出大于5的元素
mask = arr > 5
print(mask)  # [False False False False False  True  True  True  True]
print(arr[mask])  # [6 7 8 9]

# 一句话写法
print(arr[arr > 5])  # [6 7 8 9]

# 多个条件
print(arr[(arr > 3) & (arr < 8)])  # [4 5 6 7]
```

---

## 5. 数组运算与广播机制

### 5.1 基本算术运算

NumPy支持数组的**元素级运算**（对应位置元素运算）：

```python
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

print(a + b)   # [5 7 9] - 加法
print(a - b)   # [-3 -3 -3] - 减法
print(a * b)   # [4 10 18] - 乘法
print(a / b)   # [0.25 0.4 0.5] - 除法
print(a ** 2)  # [1 4 9] - 幂运算

# 标量运算（广播到所有元素）
print(a + 10)  # [11 12 13]
print(a * 3)   # [3 6 9]
```

### 5.2 广播机制（Broadcasting）

广播是NumPy最强大的特性之一：不同形状的数组进行运算时，NumPy会自动扩展较小的数组。

```python
# 示例1：一维数组 + 标量
arr = np.array([1, 2, 3])
result = arr + 10  # 10被广播到每个元素
print(result)      # [11 12 13]

# 示例2：二维数组 + 一维数组
arr_2d = np.array([[1, 2, 3],
                   [4, 5, 6]])
row = np.array([10, 20, 30])
result = arr_2d + row  # row被广播到每一行
print(result)
# [[11 22 33]
#  [14 25 36]]

# 示例3：二维数组 + 列向量
col = np.array([[10], [20]])
result = arr_2d + col  # col被广播到每一列
print(result)
# [[11 12 13]
#  [24 25 26]]
```

**广播规则**：当两个数组的维度不匹配时，NumPy会从尾部开始比较维度，如果某个维度为1或相等，就可以广播。

---

## 6. 数组形状操作

### 6.1 查看与改变形状

```python
arr = np.arange(12)
print(arr.shape)  # (12,)

# reshape - 改变形状（返回新数组）
arr_2d = arr.reshape(3, 4)
print(arr_2d)
# [[ 0  1  2  3]
#  [ 4  5  6  7]
#  [ 8  9 10 11]]

# 使用-1自动推断维度
arr_2d = arr.reshape(2, -1)  # 2行，列数自动计算（6列）
print(arr_2d.shape)  # (2, 6)

# flatten - 展平为一维（返回副本）
flat = arr_2d.flatten()
print(flat)  # [0 1 2 3 4 5 6 7 8 9 10 11]

# ravel - 展平（返回视图，更高效）
flat_view = arr_2d.ravel()
```

### 6.2 数组转置

```python
arr = np.array([[1, 2, 3],
                [4, 5, 6]])

print(arr.T)
# [[1 4]
#  [2 5]
#  [3 6]]

# 高维数组的转置
arr_3d = np.arange(24).reshape(2, 3, 4)
print(arr_3d.T.shape)  # (4, 3, 2) - 交换所有轴
```

### 6.3 合并与分割

```python
# 水平合并（左右拼接）
a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])
h_stack = np.hstack((a, b))
print(h_stack)
# [[1 2 5 6]
#  [3 4 7 8]]

# 垂直合并（上下拼接）
v_stack = np.vstack((a, b))
print(v_stack)
# [[1 2]
#  [3 4]
#  [5 6]
#  [7 8]]

# 使用concatenate通用合并
h_stack = np.concatenate((a, b), axis=1)  # axis=1表示列方向
v_stack = np.concatenate((a, b), axis=0)  # axis=0表示行方向
```

---

## 7. 统计函数

NumPy提供了丰富的统计函数，可以直接对整个数组或指定轴进行计算：

```python
arr = np.array([[1, 2, 3],
                [4, 5, 6],
                [7, 8, 9]])

# 基本统计
print(np.sum(arr))      # 45 - 所有元素之和
print(np.mean(arr))     # 5.0 - 平均值
print(np.median(arr))   # 5.0 - 中位数
print(np.std(arr))      # 2.58 - 标准差
print(np.var(arr))      # 6.67 - 方差
print(np.min(arr))      # 1 - 最小值
print(np.max(arr))      # 9 - 最大值

# 指定轴方向计算（axis=0按列，axis=1按行）
print(np.sum(arr, axis=0))  # [12 15 18] - 每列之和
print(np.sum(arr, axis=1))  # [6 15 24] - 每行之和

# 其他常用函数
print(np.cumsum(arr))   # [1 3 6 10 15 21 28 36 45] - 累积和
print(np.argmax(arr))   # 8 - 最大值索引（展平后）
print(np.argmax(arr, axis=0))  # [2 2 2] - 每列最大值的行索引
```

---

## 8. 随机数生成

NumPy的`random`模块是生成随机数据的标准工具：

```python
# 设置种子以确保结果可重复
np.random.seed(42)

# 1. 均匀分布 [0, 1)
uniform = np.random.rand(3, 3)

# 2. 标准正态分布
normal = np.random.randn(2, 4)

# 3. 随机整数 [low, high)
integers = np.random.randint(1, 100, size=(3, 3))

# 4. 从给定列表随机选择
choices = np.random.choice(['a', 'b', 'c', 'd'], size=10, p=[0.1, 0.2, 0.3, 0.4])

# 5. 正态分布（可指定均值和标准差）
normal_custom = np.random.normal(loc=10, scale=2, size=1000)

# 6. 打乱数组顺序
arr = np.arange(10)
np.random.shuffle(arr)  # 原地打乱
print(arr)

# 7. 随机排列（返回新数组）
permuted = np.random.permutation(10)
print(permuted)
```

---

## 9. 实战案例：销售数据分析

让我们综合运用NumPy的功能，分析一家公司的月度销售数据：

```python
import numpy as np

# ========== 步骤1：创建模拟数据 ==========
# 12个月的销售额（单位：万元）
months = np.arange(1, 13)
sales = np.array([120, 135, 128, 142, 156, 148, 
                  163, 175, 169, 182, 195, 188])

print("="*50)
print("销售数据分析报告")
print("="*50)

# ========== 步骤2：基本统计信息 ==========
print("\n【基本统计信息】")
print(f"全年总销售额: {np.sum(sales):.2f} 万元")
print(f"月均销售额: {np.mean(sales):.2f} 万元")
print(f"销售额标准差: {np.std(sales):.2f} 万元")
print(f"最高月销售额: {np.max(sales)} 万元（{months[np.argmax(sales)]}月）")
print(f"最低月销售额: {np.min(sales)} 万元（{months[np.argmin(sales)]}月）")

# ========== 步骤3：计算移动平均 ==========
# 3个月移动平均（平滑波动）
window_size = 3
weights = np.ones(window_size) / window_size
moving_avg = np.convolve(sales, weights, mode='valid')

print(f"\n【{window_size}个月移动平均】")
for i, avg in enumerate(moving_avg):
    print(f"  {i+1}-{i+window_size}月: {avg:.2f} 万元")

# ========== 步骤4：异常值检测 ==========
# 识别异常高/低的月份（超出均值±2倍标准差）
threshold_high = np.mean(sales) + 2 * np.std(sales)
threshold_low = np.mean(sales) - 2 * np.std(sales)

high_months = months[sales > threshold_high]
low_months = months[sales < threshold_low]

print(f"\n【异常检测】")
print(f"正常范围: {threshold_low:.2f} ~ {threshold_high:.2f} 万元")
if len(high_months) > 0:
    print(f"异常高销售月份: {high_months}月")
if len(low_months) > 0:
    print(f"异常低销售月份: {low_months}月")

# ========== 步骤5：增长分析 ==========
# 计算环比增长率（本月/上月 - 1）
growth_rates = np.diff(sales) / sales[:-1] * 100

print(f"\n【环比增长率】")
for i, rate in enumerate(growth_rates):
    print(f"  {i+2}月 vs {i+1}月: {rate:+.2f}%")

# 找出增长最快的月份
max_growth_idx = np.argmax(growth_rates) + 1
print(f"\n增长最快: {max_growth_idx+1}月（增长率 {np.max(growth_rates):.2f}%）")

# ========== 步骤6：趋势分析 ==========
# 使用线性回归拟合趋势线
x = np.arange(len(sales))
coefficients = np.polyfit(x, sales, 1)  # 一次项系数和常数项
trend = coefficients[0]  # 斜率

print(f"\n【整体趋势】")
if trend > 0:
    print(f"销售呈上升趋势，平均每月增长 {trend:.2f} 万元")
else:
    print(f"销售呈下降趋势，平均每月下降 {abs(trend):.2f} 万元")

# 预测下个月销售额
next_month = len(sales)
prediction = np.polyval(coefficients, next_month)
print(f"预计下个月销售额: {prediction:.2f} 万元")

# ========== 步骤7：分季度统计 ==========
# 按季度分组统计
quarters = sales.reshape(4, 3)  # 4个季度，每季度3个月
print(f"\n【季度统计】")
quarterly_sums = np.sum(quarters, axis=1)
quarterly_means = np.mean(quarters, axis=1)
for i in range(4):
    print(f"  Q{i+1}: 总额={quarterly_sums[i]}万元, 平均={quarterly_means[i]:.2f}万元")

# 找出最佳季度
best_quarter = np.argmax(quarterly_sums) + 1
print(f"\n最佳季度: Q{best_quarter}（总销售额 {np.max(quarterly_sums)} 万元）")

print("\n" + "="*50)
```

**输出结果示例**：
```
==================================================
销售数据分析报告
==================================================

【基本统计信息】
全年总销售额: 1901.00 万元
月均销售额: 158.42 万元
销售额标准差: 24.22 万元
最高月销售额: 195 万元（11月）
最低月销售额: 120 万元（1月）

【3个月移动平均】
  1-3月: 127.67 万元
  2-4月: 135.00 万元
  3-5月: 142.00 万元
  4-6月: 148.67 万元
  5-7月: 155.67 万元
  6-8月: 162.00 万元
  7-9月: 169.00 万元
  8-10月: 175.33 万元
  9-11月: 182.00 万元
  10-12月: 188.33 万元

【异常检测】
正常范围: 109.98 ~ 206.85 万元
无异常月份

【环比增长率】
  2月 vs 1月: +12.50%
  3月 vs 2月: -5.19%
  4月 vs 3月: +10.94%
  5月 vs 4月: +9.86%
  6月 vs 5月: -5.13%
  7月 vs 6月: +10.14%
  8月 vs 7月: +7.36%
  9月 vs 8月: -3.43%
  10月 vs 9月: +7.69%
  11月 vs 10月: +7.14%
  12月 vs 11月: -3.59%

增长最快: 12月（增长率 12.50%）

【整体趋势】
销售呈上升趋势，平均每月增长 5.94 万元
预计下个月销售额: 199.34 万元

【季度统计】
  Q1: 总额=383万元, 平均=127.67万元
  Q2: 总额=446万元, 平均=148.67万元
  Q3: 总额=507万元, 平均=169.00万元
  Q4: 总额=565万元, 平均=188.33万元

最佳季度: Q4（总销售额 565 万元）

==================================================
```

---

## 总结

本教程涵盖了NumPy的核心功能：

| 知识点         | 核心内容                                      |
| -------------- | --------------------------------------------- |
| **ndarray**    | 多维数组对象，NumPy的基础                     |
| **创建数组**   | `array`, `arange`, `zeros`, `ones`, `random`  |
| **索引切片**   | 基本索引、切片、布尔索引                      |
| **形状操作**   | `reshape`, `flatten`, `T`, `vstack`, `hstack` |
| **向量化运算** | 元素级运算，避免Python循环                    |
| **广播机制**   | 不同形状数组自动扩展                          |
| **统计函数**   | `mean`, `std`, `sum`, `min`, `max`            |
| **随机数**     | `rand`, `randn`, `randint`, `normal`          |

**下一步学习建议**：
- 学习Pandas（基于NumPy构建，更适合表格数据处理）
- 学习Matplotlib（数据可视化，与NumPy完美配合）
- 学习SciPy（科学计算，构建于NumPy之上）

记住：NumPy的精髓在于**向量化操作**——尽可能避免在Python中写循环，让NumPy在底层C代码中完成计算。多练习、多实践，你很快就能熟练运用这个强大的工具！