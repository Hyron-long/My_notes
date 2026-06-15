

# Python基础语法入门指南

Python以其简洁、易读的语法著称，被誉为“可执行的伪代码”。下面我们从最基础的元素开始，逐步构建Python的知识体系。

## 1. 你好，世界！

任何语言的第一课。在Python中，打印输出只需一行代码：

```python
print("Hello, World!")
```

- **`print()`**：Python的内置函数，用于将内容输出到控制台。
- **字符串**：`"Hello, World!"` 被双引号（或单引号`‘ ’`）包裹，表示文本数据。

## 2. 变量与数据类型

Python是**动态类型**语言，你不需要提前声明变量类型，直接赋值即可。

```python
# 整数 (int)
age = 25

# 浮点数 (float)
pi = 3.14159

# 字符串 (str)
name = "Alice"

# 布尔值 (bool)
is_student = True  # 注意大小写，True/False首字母大写

# 查看变量类型
print(type(age))   # <class 'int'>
```

**变量命名规则**：
- 只能包含字母、数字、下划线 (`_`)。
- 不能以数字开头。
- 不能使用Python关键字（如 `if`, `for`, `while`, `def` 等）。
- 惯例使用蛇形命名法：`my_var_name`。

## 3. 基本输入与类型转换

- **`input()`**：从用户获取输入，**返回值永远是字符串**。

```python
# 获取输入
user_input = input("请输入你的年龄：")

# 转换为整数
age = int(user_input)

# 一年后的年龄
age_next_year = age + 1
print("明年你将", age_next_year, "岁")

# 一次性转换并输入
height = float(input("请输入身高（米）："))
```

## 4. 简单数据结构

### 列表 (List)
有序、可变、可重复的集合，用方括号 `[]` 表示。

```python
fruits = ["apple", "banana", "cherry"]
print(fruits[0])    # 索引从0开始，输出：apple
print(fruits[-1])   # 负数索引从末尾开始，输出：cherry

# 常用操作
fruits.append("orange")   # 末尾添加
fruits.insert(1, "grape") # 在索引1处插入
fruits.remove("banana")   # 删除元素
print(len(fruits))        # 获取长度：3
```

### 元组 (Tuple)
有序、**不可变**的集合，用圆括号 `()` 表示。内容创建后不能修改。

```python
coordinates = (10, 20)
x, y = coordinates   # 元组解包
print(x, y)          # 输出：10 20
```

### 字典 (Dictionary)
键值对集合，用花括号 `{}` 表示，通过键来访问值。

```python
person = {
    "name": "Bob",
    "age": 30,
    "city": "New York"
}

print(person["name"])    # 输出：Bob
person["job"] = "Engineer"  # 添加新键值对

# 安全访问（避免键不存在时报错）
print(person.get("height", "未提供")) # 输出：未提供
```

## 5. 控制流

### 条件判断 (if-elif-else)

```python
score = 85

if score >= 90:
    print("优秀")
elif score >= 80:
    print("良好")   # 此条件满足，会执行
elif score >= 60:
    print("及格")
else:
    print("不及格")

# 注意缩进！Python使用缩进来定义代码块，通常为4个空格。
```

### 循环

#### `for` 循环（遍历可迭代对象）

```python
# 遍历列表
for fruit in fruits:
    print(fruit)

# 配合 range() 函数生成数字序列
for i in range(5):      # range(5) 生成 0,1,2,3,4
    print(i)

for i in range(2, 10, 2): # 从2开始到9，步长为2 -> 2,4,6,8
    print(i)
```

#### `while` 循环（条件满足时重复）

```python
count = 0
while count < 3:
    print("循环次数:", count)
    count += 1   # 小心无限循环！
```

#### `break` 和 `continue`

- **`break`**: 立即终止整个循环。
- **`continue`**: 跳过本次循环剩余代码，进入下一次迭代。

```python
for num in range(10):
    if num == 3:
        continue   # 跳过3，不打印
    if num == 7:
        break      # 遇到7，循环结束
    print(num)     # 输出：0,1,2,4,5,6
```

## 6. 函数

使用 `def` 关键字定义函数，提高代码复用性。

```python
# 定义函数
def greet(name):
    """这是一个文档字符串，描述函数功能"""  # 可选但推荐
    return f"你好，{name}！"

# 调用函数
message = greet("小明")
print(message)  # 输出：你好，小明！

# 带默认参数值的函数
def power(base, exponent=2):
    return base ** exponent

print(power(3))     # 使用默认指数2 -> 9
print(power(3, 3))  # 传入指数3 -> 27
```

## 7. 注释

注释不会被Python执行，用于解释代码或临时禁用代码。

```python
# 这是单行注释

"""
这是多行注释
实际是未被赋值的字符串，
但常被用作文档字符串（docstring）
"""
```

## 8. 综合实例：温度转换器

让我们把上述知识点结合起来，写一个完整的实用小工具。

```python
def celsius_to_fahrenheit(celsius):
    """摄氏度转华氏度"""
    return celsius * 9/5 + 32

def fahrenheit_to_celsius(fahrenheit):
    """华氏度转摄氏度"""
    return (fahrenheit - 32) * 5/9

def main():
    print("=== 温度转换器 ===")
    print("1. 摄氏度 -> 华氏度")
    print("2. 华氏度 -> 摄氏度")

    choice = input("请选择转换方式 (1/2): ")

    if choice == "1":
        temp_c = float(input("输入摄氏度: "))
        temp_f = celsius_to_fahrenheit(temp_c)
        print(f"{temp_c}°C 等于 {temp_f:.2f}°F")  # :.2f 保留两位小数

    elif choice == "2":
        temp_f = float(input("输入华氏度: "))
        temp_c = fahrenheit_to_celsius(temp_f)
        print(f"{temp_f}°F 等于 {temp_c:.2f}°C")

    else:
        print("无效输入，请输入1或2")

# 程序入口点
if __name__ == "__main__":
    main()
```

## 总结与下一步建议

你已经掌握了Python的核心基础语法：

| 概念     | 关键词/符号                                            |
| :------- | :----------------------------------------------------- |
| 输出     | `print()`                                              |
| 输入     | `input()`                                              |
| 数据类型 | `int`, `float`, `str`, `bool`, `list`, `tuple`, `dict` |
| 条件判断 | `if`, `elif`, `else`                                   |
| 循环     | `for`, `while`, `range()`                              |
| 函数定义 | `def`, `return`                                        |

**进阶学习方向**：
- **文件操作**：`open()` 读写文件
- **异常处理**：`try-except` 捕获错误
- **模块与包**：`import` 使用第三方库
- **面向对象**：`class` 定义类和对象

