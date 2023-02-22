# Python学习笔记

## 基础方法

```python
print(text)

input(prompt) 
#返回输入的值

len(str) 
#读取str的长度，返回int

str1 in str2 
#判断str2中是否含有str1，返回bool

str.upper()
str.lower()

str1.find(str2) 
#在str1中查找str2，返回str2第一个字符位置的int

str1.replace(str2, str3) 
#在str1中将所有str2替换为str3，返回替换完成的str

```

## 算术

|运算|符号/方法|
|:-|:-|
|求幂exponentiation| **|
|乘multiplication|*|
|除division|/|
|除，向下取整|//|
|取余|%|
|加addition|+|
|减subtraction|-|
|四舍五入|round()|  
|绝对值|abs()|

高级：Math Module (先`import math`)  


## If语句

```python
if condition:
	#action if condition is true
elif another_condition:
	#action if another_condition is true
else:
	#action if both are false
```


## While循环

```python
while condition:
	action if condition is true
	break
else:
```

### For循环

```python
for item in [1,2,3,4]:
	action

for item in range(4):
	action
```

## 数组方法

```python
sort, reverse, pop ...
```


## 元组（Tuple）

元组的拆包

```python
coordinates = (1, 2, 3)
x, y, z = coordinates
```

## 数据对
	
```python
customer = {
	"name": "John Smith",
	"age": 30,
	"is_verified": True
}
print(customer['name'])
customer.get("name", "default output")
```


## 声明方法

```python
def name_of_method(first_argument, second_argument):
	do something
	return something
```


#### 赋予初始变量的两种方法：

```python
name_of_method('1','2')
#positional argument

name_of_method(second_argument='2', first_argument='1')
#keyword argument
```

## try结构

```python
try:
	action
except nameOfException:
	action	
```

## 声明类

```python
class ClassName:
	def __init__(self, x, y):
	#__init__ is a "constructor"
		self.x = x
		self.y = y
	def ...
```

```python
#像调用方法一样调用类。（实例化？）

	class1 = ClassName(x1, y1) Python

#例子：

	class Person:
		def __init__(self, name):
			self.name = name

		def talk(self, speech):
			print(f"{self.name}: '{speech}'")


	person1 = Person(input("Name: "))
	person1.talk(input("Content of Speech: "))
```

## 类的继承

将被继承的类作为新声明的类的变量？

```python
class Mammel:
	def walk(self):
		print("walk")

class Dog(Mammel):
	pass

dog = Dog()
dog.walk()
```
## From/Import

```python
#假设要调用某个package内的某个module中的一个方法:
import packageName.moduleName
packageName.moduleName.functionName()

#或者
from packageName import moduleName
moduleName.functionName()	

#或者
from packageName.moduleName import functionName, function2...
functionName()

#也可以为引用的package设置简称
import packageName as alias
```

## Python的内建模块

#### random模块

```python
random.random() #生成0到1之间的随机数
random.randint(x, y) #生成x到y之间的随机整数
ramdom.choice(list) #从list中随机选取一项
```

#### pathlib模块

```python
from pathlib import Path

path = Path("name")
path.exists() #根据name目录是否存在返回bool
path.mkdir() #新建目录
path.rmdir() #删除目录
```

## PyPI.org上的库

登陆pypi.org可以查找到众多公共的库，例如：

#### openpyxl

openpyxl是python用来执行与工作表（.xls, .xlsx）相关操作的库
在PyCharm中的Terminal执行`pip install openpyxl`即可安装

## openpyxl的用法

```python
import openpyxl as xl
from openpyxl.chart import BarChart, Reference

wb = xl.load_workbook("transactions.xlsx") #载入工作簿
sheet = wb["Sheet1"] #载入工作表
cell = sheet["a1"] #载入A1单元格
cell = sheet.cell(1, 1)	#载入第一行第一列的单元格（和上一行语句相同）

for row in range(2, sheet.max_row + 1):
	cell = sheet.cell(row, 3)
	corrected_price = cell.value * 0.9
	corrected_price_cell = sheet.cell(row, 4)
	corrected_price_cell.value = corrected_price

values = Reference(sheet,
		min_row=2,
		max_row=sheet.max_row,
		min_col=4,
		max_col=4)

chart = BarChart()
chart.add_data(values)
sheet.add_chart(chart, 'e2')

wb.save("transaction2.xlsx")
```
