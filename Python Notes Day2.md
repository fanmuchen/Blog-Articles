# 机器学习

## Jupyter 用法

先安装 Anaconda:

anaconda.com

然后在 Terminal 中运行：

```shell
jupyter notebook
```

Jupyter notebook 会在网页浏览器中打开。

另外，推荐一个数据网站 kaggle.com

asdfasdfasdfasfdasdfsdfaasdf

## 机器学习的实用库

### pandas

```python
import pandas

data = pandas.read_csv(fileName)
#读取CSV文件，实例化一个数据列表对象
X = data.drop(columns = [列首])
#移除列表的某一列，返回一个新的数据表，本身不影响
y = data[列首]
#提取列表中的某一列
```

### sklearn (Sidekick Learn)

接上一段代码

```python
from sklearn.tree import DecisionTreeClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)
#将原始数据集按指定尺寸分为训练数据集和测试数据集

model = DecisionTreeClassifier()
model.fit(X_train , y_train)
#训练模型
predictions = model.predict(X_test)
#用测试数据集的自变量预测结果
score = accuracy_score(y_test, predictions)
#计算测试的预测结果的准确性
```

### Joblib

接上一段代码

```python
import joblib
#老版本则是 from sklearn.externals import joblib

joblib.dump(model, 'savedModel.joblib')
#将训练好的model存储为.joblib文件形式
```

然后可以导入

```python
model = joblib.load('savedModel.joblib')
predictions = model.predict([[xxx, yyy]])
...
```

### Decision Tree 的可视化

接上一段代码。以 Mosh 提供的 music.csv 作为数据集进行学习为例：

```python
	from sklearn import tree
	tree.export_graphviz(model, out_file='music-recommender.dot',
                    	feature_names=['age', 'gender'],
                    	class_names=sorted(y.unique()),
                    	label='all',
                    	rounded=True,
                    	filled=True)
```

导出的.dot 文件可以在 vs code 中打开查看。

# 搭建 Web 服务器

## 准备工作

先在 PyCharm 新建一个 Python 项目，

安装 Django，在 Terminal 中运行，下同：

```shell
pip install django==版本号
#==版本号可省略
```

新建 Django 项目：

```shell
django-admin startproject pyshop .
#最后的点指示项目将创建在当前目录
#pyshop即为Django项目的名称，当然可以是任何名字
```

启动项目：

```shell
python3 manage.py runserver
```

至此，Django 服务器已经启动，可以在网页浏览器中通过 127.0.0.1:8000 来访问了。

## 建立一个 app

为项目新建一个 app：

```shell
python3 manage.py startapp products
```

名为 products 的 package 将被添加到本项目的根目录下。

products 内的 views.py（自动生成）决定了展示给访问者的内容，例如，在 views.py 中写入：

```python
from django.http import HttpResponse
from django.shortcuts import render

def index(request):
	return HttpResponse('Hello World')
```

访问者在打开这个 app 时就会看见纯文本 Hello World。

为了能访问这个 app，还需要做好 url 映射（mapping）。步骤如下：

在 products 目录下新建 urls.py，

```python
from django.urls import path
from . import views

urlpatterns = [
	path('', views.index),
]
```

在 pyshop 目录（即为 Django 项目名称的目录，应与 products 目录同级，是创建 Django 项目时自动生成的）下找到 urls.py，

```python
from django.contrib import admin
from django.urls import path, include
#include是手动添加的方法

urlpatterns = [
	path('admin/', admin.site.urls),
	path('products/', include('products.urls')) #手动添加这一行
]
```

至此，url 映射设置完毕，此时访问`127.0.0.1:8000/products/`，应显示“Hello World”。

## 在 App 中建立一个 Model

例如，要建立一个“产品”类，这个类能储存产品的各种信息。

在 products package 中的 model.py 中：

```python
from django.db import models

class Product(models.Model):
	name = models.CharField(max_length=255)
	price = models.FloatField()
	stock = models.IntegerField()
	image_url = models.FloatField(max_length=2083)
```

###在 Django 项目中设置 App

本步骤是利用 makemigration 指令将 model 迁移到数据库中的必要条件。

在 pyshop 目录下的 settings.py 中，找到 INSTALLED_APPS 数列，加入：

```python
products.apps.ProductsConfig
#该类是在products目录下的apps.py中定义的
```

### migration 指令

```shell
python3 manage.py makemigrations
python3 manage.py migrate
```

## 使用 Django 自带的控制台

在 Terminal 中设置超级用户（superuser）

```shell
python3 manage.py createsuperuser
```

根据指引操作。设置完成后在浏览器中访问`127.0.0.1:8000/admin`即可访问控制台。

### 将特定 model 纳入控制台的管理

在 products 目录下找到 admin.py，写入：

```python
from .models import Product

admin.site.register(Product)
```

或者，定义一个 ModelAdmin 的子类，自定义在控制台管理 Model 的方式：

```python
from django.contrib import admin #自动生成的引用

class ProductAdmin(admin.ModelAdmin):
	list_display = ('name', 'price', 'stock')

admin.site.register(Product, ProductAdmin)
#指定使用定义好的ProductAdmin类来管理Product类
```

## 建立模板

在 products 目录下建立新目录，命名为 Templates（惯例）

在 Templates 下建立新文件 index.html，写入：

```html
<h1>Products</h1>
<ul>
  {% for product in products %}
  <li>{{ product.name }} ({{ product.price }})</li>
  {% endfor %}
</ul>
```

其中，`{% %}`和`{{ }}`是 Django 的语法，可以动态地渲染传递来的数据。

同时，在 products 的目录下的 view.py，修改 index 方法：

```python
def index(request):
	products = Product.objects.all()
	#从数据库读取Product类中的所有项目
	return render(request, 'index.html', {'products': products})
	#将products传递到index.html模板中
```

此时，访问`127.0.0.1:8000/products/`，可以浏览设置好的页面。

## 利用 Bootstrap 来修饰页面

访问 getbootstrap.com，找到 Starter template，复制代码。

回到 PyCharm，在 template 目录中，新建 base.html，黏贴代码。

在 body 部分，填写内容：

```html
{% block content %} {% endblock %}
```

用来动态地显示我们提供的 block 内容。

回到 index.html，做如下调整：

```html
{% extends 'base.html' %} {% block content %}

<h1>Products</h1>
<ul>
  {% for product in products %}
  <li>{{ product.name }} ({{ product.price }})</li>
  {% endfor %}
</ul>

{% endblock %}
```

访问`127.0.0.1:8000/products/`，页面的观感并没有较大的改善，但此时看到的页面已经是 Bootstrap 提供的框架。现在可以利用 Bootstrap 的组件了。

访问 getbootstrap.com --> docs --> components --> card --> 复制代码，插入到 index.html 中，用 div 语句块取代原先的 ul 语句块，并完成动态变量的设置。完成后：

```html
{% extends 'base.html' %} {% block content %}

<h1>Products</h1>
<div class="row">
  {% for product in products %}
  <div class="col">
    <div class="card" style="width: 18rem;">
      <img src="{{ product.image_url }}" class="card-img-top" alt="..." />
      <div class="card-body">
        <h5 class="card-title">{{ product.name }}</h5>
        <p class="card-text">${{ product.price }}</p>
        <a href="#" class="btn btn-primary">Add to Cart</a>
      </div>
    </div>
  </div>
  {% endfor %}
</div>

{% endblock %}
```

访问`127.0.0.1:8000/products/`,发现商品卡片已经生成，并且可以随着页面的尺寸变化而自动调整了。

但还有一些细节需要调整。回到 base.html，将原来的 body 部分更改为：

```html
<nav class="navbar navbar-light bg-light">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">PyShop</a>
  </div>
</nav>
<div class="container">{% block content %} {% endblock %}</div>
```

其中 nav 语句块是 Bootstrap 提供的导航栏，div 语句块则是 Bootstrap 提供的调整主体部分位置的工具。

### 复用模板

现在 Base.html 位于 Products 下的 templates 目录内，仅能在 Products 中被调用。如果我们需要在整个网站调用这个模板，则需要将其移至项目根目录下的 templates 目录内（如无，需手动创建），同时在 PyShop 目录下的 settings.py 中添加路径：

```python
import os

TEMPLATES = [
	{
		...
		'DIRS' : [
		os.path.join(BASE_DIR, 'templates')
		]
		...
	}
]
```
