---
title: python面向对象-特殊属性、查看属性、魔术方法
date: 2017-10-11
categories: python
tags: python
---

### 特殊属性

| 属性        | 含义                                             |
| ----------- | ------------------------------------------------ |
| `__name_`   | 类、函数、方法等的名字                           |
| `__module_` | 类定义所在的模块名                               |
| `__class_`  | 对象或者类所属的类                               |
| `__bases__` | 类的基类的元组，顺序为它们在基类列表中出现的顺序 |
| `__doc__`   | 类、函数的文档字符串，如果没有定义则为None       |
| `__mro__`   | 类的mro，class.mro()返回的结果保存在`__mro__`中  |
| `__dict__`  | 类的实例的属性，可写的字典                       |

### 查看属性

| 方法      | 意义                                                                                                                                                                                                                       |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `__dir__` | 返回类或者对象的所有成员名称列表。dir()函数就是调用\_\_dir\_\_()。 <br/>使用实例调用时，如果提供_\_dir\_\_()，则返回其返回值，要求是可迭代对象。 如果没有提供\_\_dir\_\_()，则会从实例和类及祖先类中收集信息（尽可能多的） |

如果dir([obj])参数obj包含方法\_\_dir\_\_()，该方法将被调用。如果参数obj不包含\_\_dir\_\_()，该方法将最大限
度地收集属性信息。

dir(obj)对于不同类型的对象obj具有不同的行为：

1. 如果对象是模块对象，返回的列表包含模块的属性名和变量名。

2. 如果对象是类或者类对象，返回的列表包含类的属性名，及它的基类的属性名。

3. 如果obj不写，返回列表包含内容不同
   - 在模块中，返回模块的属性和变量名
   - 在函数中，返回本地作用域的变量名
   - 在方法中，返回本地作用域的变量名

## <font color=red>魔术方法</font>

------

分类：

- 创建、初始化和销毁
  - `__new__`和`__init__`和`__del__`
- hash
- bool
- 可视化
- 运算符重载
- 容器和大小
- 可调用对象
- 上下文管理
- 反射
- 描述器
- 其他

#### 实例化

------

| 方法      | 意义                                                                                                                    |
| --------- | ----------------------------------------------------------------------------------------------------------------------- |
| `__new__` | 实例化一个对象<br>该方法需要返回一个值(本类型的实例)，如果该值不是cls的实例，则不会调用`__init__`<br>该方法永远都是静态 |

\_\_new\_\_方法很少使用，即使创建了该方法，也会使用return super().\_\_new\_\_(cls)得到实例化对象，或者基类object的\_\_new\_\_方法来创建实例并返回。

#### hash

------

| 方法       | 意义                                                                           |
| ---------- | ------------------------------------------------------------------------------ |
| `__hash__` | 内建函数hash()调用的返回值，返回一个整数。如果定义这个方法该类的实例就可hash。 |
| `__eq__`   | 对应==操作符，判断2个对象是否相等，返回bool值                                  |

```python
class A:
    def __init__(self, name, age=18):
        self.name = name
 
    def __hash__(self):
        return 1
 
    def __eq__(self, other):
        return self.name == other.name
 
    def __repr__(self):
        return self.name
 
print(hash(A('tom')))
print((A('tom'), A('tom')))
print([A('tom'), A('tom')])
print({('tom',), ('tom',)})
```

hash(x) ,x都一样，求得的hash应该是不变的，这是幂等性的缘故，一般来说，x不一样，hash应该不一样。

不同的hash算法，不同的x求得同样的hash值，这就是hash冲突。

------

`__hash__`方法只是返回一个hash值作为set的key，但是去重，还需要`__eq__`（等效==，就是内容相等）来判断2个对象是否相等，is判断的是内存地址，is相等的话就肯定是同一个元素。
hash值相等，只是hash冲突，不能说明两个对象是相等的。 
因此，一般来说提供`__hash__`方法是为了作为set或者dict的key，所以去重要同时提供`__eq__`方法。

不可hash对象isinstance(p1, collections.Hashable)一定为False。 
去重需要提供`__eq__`方法。

------

list类实例为什么不可hash 

源码中有一句`__hash__` = None，也就是如果调用`__hash__()`相当于None()，一定报错。 
所有类都继承object，而这个类是具有`__hash__()`方法的，如果一个类不能被hash，就把`__hash__`设置为None。

#### bool

------



| 方法       | 意义                                                                                                                                                                                                                                  |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `__bool__` | 内建函数bool()，或者对象放在逻辑表达式的位置，调用这个函数返回布尔值。 定义`__bool__()`，这个函数的返回值必须要是bool类型。没有定义`__bool__()`，就找`__len__()`返回长度，非0为真。 如果`__len__()`也没有定义，那么所有实例都返回真。 |

两个对象（类和实例）可以当作True理解。

等效Fals的本质是：对于四大皆空：空串，空元组。空列表，空字典，先找bool，如果没有bool就看长度，当长度为0就恒为假。

#### 可视化

------

| 方法        | 意义                                                         |
| ----------- | ------------------------------------------------------------ |
| `__repr__`  | 内建函数repr()对一个对象获取字符串表达。 <br/>调用`__repr__`方法返回**字符串**表达，如果`__repr__`也没有定义，就直接返回object的定义，就是显示内存地址信息。 |
| `__str__`   | str()函数、format()函数、print()函数调用，需要返回对象的字符串表达。如果没有定义，就去调用`__repr__`方法返回**字符串**表达，如果`__repr__`没有定义，就直接返回对象的内存地址信息。 |
| `__bytes__` | bytes()函数调用，返回一个对象的bytes表达，即返回bytes对象    |

```python
class A:
    def __init__(self, name, age=18):
        self.name = name
        self.age = age
 
    def __repr__(self):
        return 'repr: {},{}'.format(self.name, self.age)
 
    def __str__(self):
        return 'str:  {},{}'.format(self.name, self.age)
 
    def __bytes__(self):
        #return "{} is {}".format(self.name, self.age).encode()
        import json
        return json.dumps(self.__dict__).encode()
 
 
print(A('tom')) # print函数使用__str__
print([A('tom')]) # []使用__str__，但其内部使用__repr__
print([str(A('tom'))]) # []使用__str__，其中的元素使用str()函数也调用__str__
print(bytes(A('tom')))
```
总结：一般首先找的是repr，当repr没有就找str，都没有就去object中去寻找。bytes方法转成二进制只是一种序列化的表达，和序列化还是有差别的，序列化是一种通用的二进制格式或者通用的中间格式，序列化是一种交互，转化成文本传输的，而这里的转换只是一种表达，给人展示用的。

> 注意不能通过判断是否带引号来判断输出值的类型，类型判断要使用type或isinstance

### 运算符重载

------

operator模块提供了以下特殊方法，可以将类的实例使用下面的操作符来操作

| 运算符                          | 特殊方法                                                                                           | 含义       |
| ------------------------------- | -------------------------------------------------------------------------------------------------- | ---------- |
| <, <=, ==, >, >=, !=            | `__lt__`, `__le__`, `__eq__`, `__gt__`, `__ge__`, `__ne__`                                         | 比较运算符 |
| +, -, \*, /, %, //,\*\*, divmod | `__add__`, `__sub__`, `__mul__`, `__truediv__`, `__mod__`, `__floordiv__`, `__pow__`, `__divmod__` | 算数运算符 |
| +=, -=, \*=, /=, %=, //=, \*\*= | `__iadd__`, `__isub__`, `__imul__`, `__itruediv__`, `__imod__, __ifloordiv__`,` __ipow__`          |            |

#### @functools.total_ordering 装饰器 

\_\_lt\_\_, _\_le\_\_, _\_eq\_\_, _\_gt\_\_, _\_ge\_\_是比较大小必须实现的方法，但是全部写完太麻烦，使用
@functools.total_ordering 装饰器就可以大大简化代码。

但是要求\_\_eq\_\_必须实现，其它方法\_\_lt\_\_, \_\_le\_\_, \_\_gt\_\_, \_\_ge\_\_  实现其一。

但是：

\_\_eq\_\_等于可以推断不等于 
\_\_gt\_\_大于可以推断小于 
\_\_ge\_\_大于等于可以推断小于等于 
也就是用3个方法，就可以把所有比较解决了，所以total_ordering可以不使用

### 容器相关方法

------

| 方法           | 意义                                                         |
| -------------- | ------------------------------------------------------------ |
| `__len__`      | 内建函数len()，返回对象的长度（>=0的整数），如果把对象当做容器类型看，就如同list或者dict。bool()函数调用的时候，如果没有\_\_bool\_\_()方法，则会看\_\_len\_\_()方法是否存在，存在返回非0为真。 |
| `__iter__`     | 迭代容器时，调用，返回一个新的迭代器对象                     |
| `__contains__` | in 成员运算符，没有实现，就调用`__iter__`方法遍历            |
| `__getitem__`  | 实现self[key]访问。序列对象，key接受整数为索引，或者切片。对于set和dict，key为hashable。key不存在引发KeyError异常 |
| `__setitem__`  | 和`__getitem__`的访问类似，是设置值的方法                    |
| `__missing__`  | 字典或其子类使用`__getitem__`()调用时，key不存在执行该方法   |

```python
class A(dict):
    def __missing__(self, key):
        print('Missing key : ', key)
        return 0
 
a = A()
print(a['k'])
```

### 可调用对象 

------

```python
def foo():
    print(foo.__module__, foo.__name__)
 
foo()
# 等价于
foo.__call__()
```

函数即对象，对象foo加上()，就是调用此函数对象的`__call__`()方法。

**可调用对象**

| 方法       | 意义                                       |
| ---------- | ------------------------------------------ |
| `__call__` | 类中定义一个方法，实例就可以像函数一样调用 |

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
 
    def __call__(self, *args, **kwargs):
        return "<Point {}:{}>".format(self.x, self.y)
 
p = Point(4, 5)
print(p)
print(p())
 
class Adder:
    def __call__(self, *args):
        ret = 0
        for x in args:
            ret += x
        self.ret = ret
        return ret
 
adder = Adder()
print(adder(4, 5, 6))
print(adder.ret)


#<__main__.Point object at 0x000002D5F04C2198>
#<Point 4:5>
#15
#15
```

练习:

定义一个斐波那契数列的类，方便调用，计算第n项

```python
class Fib:
    def __init__(self):
        self.items = [0, 1, 1]
 
    def __call__(self, index):
        if index < 0:
            raise IndexError('Wrong Index')
        if index < len(self.items):
            return self.items[index]
 
        for i in range(3, index+1):
            self.items.append(self.items[i-1] + self.items[i-2])
        return self.items[index]
 
print(Fib()(100))
```

上例中，增加迭代的方法、返回容器长度、支持索引的方法

```python
class Fib:
    def __init__(self):
        self.items = [0, 1, 1]

    def __call__(self, index):
        return self[index]

    def __iter__(self):
        return iter(self.items)

    def __len__(self):
        return len(self.items)

    def __getitem__(self, index):
        if index < 0:
            raise IndexError("Wrong Index")
        if index < len(self.items):
            return self.items[index]

        for i in range(len(self), index + 1):
            self.items.append(self.items[i - 1] + self.items[i - 2])
        return self.items[index]

    def __str__(self):
        return str(self.items)

    __repr__ = __str__


fib = Fib()
print(fib(5), len(fib))  # 全部计算
print(fib(10), len(fib))  # 部分计算
print("------")
for x in fib:
    print(x, end=" ")

print()
print(fib[5], fib[9])  # 索引访问，不计算
```

### 上下文管理对象

------

当一个对象同时实现了`__enter__`（）和`__exit__`（）方法，它就属于上下文管理的对象。

| 方法        | 意义                                                         |
| ----------- | ------------------------------------------------------------ |
| `__enter__` | 进入与此对象相关的上下文。如果存在该方法，with语法会把该方法的返回值作为绑定到as子句中指定的变量上 |
| `__exit__`  | 退出与此对象相关的上下文。                                   |

```python
class Point:
    def __init__(self):
        print("init")

    def __enter__(self):
        print("enter")

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("exit")


with Point() as f:
    print("-" * 30)
    raise Exception('error')

print("=====end=======")
```

实例化对象的时候，并不会调用enter，进入with语句块调用\_\_enter\_\_方法，然后执行语句体，最后离开with语句块的时候，调用\_\_exit\_\_方法。

with可以开启一个上下文运行环境，在执行前做一些准备工作，执行后做一些收尾工作。 
注意，with并不开启一个新的作用域。

上下文管理很安全，不管是碰到异常都还是会正常执行

`__enter__`方法返回值就是上下文中使用的对象，with语法会把它的返回值赋给as子句的变量。

#### 方法的参数

------

`__enter__`方法 没有其他参数。
`__exit__`方法有3个参数： 
`__exit__`(self, exc_type, exc_value, traceback) 
这三个参数都与异常有关。 
如果该上下文退出时没有异常，这3个参数都为None。 
如果有异常，参数意义如下 
exc_type，异常类型 
exc_value，异常的值 
traceback，异常的追踪信息 
`__exit__`方法返回一个等效True的值，则压制异常；否则，继续抛出异常

#### 上下文应用场景 
---
1. 增强功能 
    在代码执行的前后增加代码，以增强其功能。类似装饰器的功能。
2. 资源管理 
    打开了资源需要关闭，例如文件对象、网络连接、数据库连接等
3. 权限验证 
    在执行代码之前，做权限的验证，在`__enter__`中处理

#### contextlib.contextmanager

------

contextlib.contextmanager 
它是一个装饰器实现上下文管理，装饰一个函数，而不用像类一样实现\_\_enter\_\_和\_\_exit\_\_方法。 
对下面的函数有要求，必须有yield，也就是这个函数必须返回一个生成器，且只有yield一个值。 
也就是这个装饰器接收一个生成器对象作为参数。

```python
import contextlib

@contextlib.contextmanager
def sub(x, y):  # 为生成器函数增加了上下文管理
    print("enter")
    start = datetime.datetime.now()
    try:
        yield x - y  # yield的值只能有一个，作为__enter__方法的返回值
    finally:
        detla = (datetime.datetime.now() - start).total_seconds()
        print(detla)
        print("exit")

with sub(6, 2) as f:
    time.sleep(2)
    print("------------")
    print(f)
    print("~~~~~~~~~~")
    
#输出
enter
------------
4
~~~~~~~~~~
2.000654
exit
```

当yield发生处为生成器函数增加了上下文管理。这是为函数增加上下文机制的方式。

- 把yield之前的当做\__enter__方法执行
- 把yield之后的当做\__exit__方法执行
- 把yield的值作为\__enter__的返回值

---
总结 :
如果业务逻辑简单可以使用函数加contextlib.contextmanager装饰器方式，如果业务复杂，用类的方式加\_\_enter\_\_和\_\_exit\_\_方法方便。