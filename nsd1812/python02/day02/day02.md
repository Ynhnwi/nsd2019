# py02_day02

## 函数

函数创建没有先后关系，只要在调用时，所有的函数已经存在即可

```python
>>> def foo():
...     print('in foo')
...     bar()
... 
>>> def bar():
...     print('in bar')
... 
>>> foo()
in foo
in bar
```

### 函数参数

- 只有一个参数名，称作位置参数
- 参数形式是key=val，称作关键字参数

```python
>>> def get_age(name, age):
...     print('%s is %s years old' % (name, age))
... 
>>> get_age('bob', 20)   # OK
bob is 20 years old
>>> get_age()   # Error，参数太少
>>> get_age('bob', 20, 100)   # Error，参数太多
>>> get_age(20, 'bob')  # 语法没有问题，但是语义不对
20 is bob years old
>>> get_age(age=20, name='bob')  # OK
bob is 20 years old
>>> get_age(age=20, 'bob')  # Error，关键字参数必须在后
>>> get_age(20, name='bob')  # Error，name得到了多个值
>>> get_age('bob', age=20)   # OK
bob is 20 years old
```

### 参数组

如果函数的参数个数是不固定的，可以使用参数组接收参数

- 在参数名前加\*表示参数是元组
- 在参数名前加\*\*表示参数是字典

```python
>>> def func1(*args):
...     print(args)
... 
>>> func1()
()
>>> func1('bob')
('bob',)
>>> func1('bob', 123)
('bob', 123)
>>> func1('bob', 123, 'hello', 'aaa')
('bob', 123, 'hello', 'aaa')
>>> def func2(**kwargs):
...     print(kwargs)
... 
>>> func2()
{}
>>> func2(name='bob', age=20)
{'name': 'bob', 'age': 20}
```

调用函数时，如果参数有\*号，表示把参数拆开。

```python
>>> def add(x, y):
...     print(x + y)
... 
>>> nums = [10, 20]
>>> num_dict = {'x': 100, 'y': 200}
>>> add(nums)  # Error，因为nums传给了x，y没有得到值
>>> add(num_dict)  # Error，同上
>>> add(*nums)  # nums拆成了10, 20
30
>>> add(**num_dict)  # 拆成了x=100, y=200
300
```

### 加减法小程序

```shell
5 + 5 = 10
Very Good!!!
Continute(y/n)? y
85 - 23 = 1
Wrong answer.
85 - 23 = 10
Wrong answer.
85 - 23 = 30
Wrong answer.
85 - 23 = 62  (系统给出正确答案)
Continute(y/n)? n
Bye-bye
```

### 匿名函数

如果函数的代码块非常简单，只有一行，可以使用匿名函数。

匿名函数使用lambda关键字定义。

```python
>>> def add(x, y):
...     return x + y
... 
>>> lambda x, y: x + y
<function <lambda> at 0x7f0b96735c80>
>>> 
>>> myadd = lambda x, y: x + y
>>> myadd(10, 20)
30
```

### filter函数

filter函数可以接受两个参数，第一个参数是函数，它必须返回真或假；第二个参数是个可迭代对象。filter的作用是，将第二个参数中的每一项交给第一个参数（函数）处理，返回值为真保留，否则过滤。

### map函数

map函数可以接受两个参数，第一个参数是函数，第二个参数是个可迭代对象。将可迭代对象中的每项交由map处理，处理的结果保存下来。

### 变量

#### 全局变量

在函数外面定义的变量。从定义起始位置开始，一直到程序结束，任何地方均可见可用。

#### 局部变量

在函数内部定义的变量。只在函数内部使用，离开函数不能使用。函数的参数也可以看作是局部变量。

```python
>>> x = 10
>>> def fn1():
...     print(x)
... 
>>> fn1()
10
>>> def fn2():
...     x = 'hello'
...     print(x)  # 局部和全局有相同的变量，局部遮盖住全局变量
... 
>>> fn2()
hello
>>> x
10
>>> def fn3():
...     global x  # 声明函数内部使用的是全局变量x
...     x = 'ni hao'
...     print(x)
... 
>>> fn3()
ni hao
>>> x
'ni hao'
```

名称查找的时候，先查局部，再查全局，最后查内建。

```python
>>> def fn4():
...     print(len('abc'))
... 
>>> fn4()  # 查到了内建len函数
3
>>> len = 10
>>> fn4()  # 在全局查到len值是10，数字不能调用
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in fn4
TypeError: 'int' object is not callable
```

### 偏函数

可以理解为，改造现有函数，将现有函数中的一些参数赋值，生成新函数。

```python
>>> def add(a, b, c, d, e):
...     return a + b + c + d + e
... 
>>> add(10, 20, 30, 40, 5)
105
>>> add(10, 20, 30, 40, 8)
108
>>> add(10, 20, 30, 40, 13)
113
>>> from functools import partial
>>> myadd = partial(add, 10, 20, 30, 40)
>>> myadd(5)
105
>>> myadd(8)
108
```










