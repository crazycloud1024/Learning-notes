---
title: python-反射、描述器
date: 2017-10-19
categories: python
tags: python
---

## 反射

### 概述

运行时，区别于编译时，指的是程序被加载到内存中执行的时候。 
反射，reflection，指的是运行时获取类型定义信息。 
简单说，在Python中，能够通过一个对象，找出其type、class、attribute或method的能力，称为反射或者自省。 
具有反射能力的函数有：type()、isinstance()、callable()、dir()、getattr()

| 内建函数                         | 意义                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| getattr(object, name[, default]) | 通过name返回object的属性值。当属性不存在，将使用default返回，如果没有default，则抛出AttributeError。name必须为字符串 |
| setattr(object, name, value)     | object的属性存在，则覆盖，不存在，新增                       |
| hasattr(object, name)            | 判断对象是否有这个名字的属性，name必须为字符串               |

给一个实例用setattr添加一个方法，不做绑定，不会绑定到类中，此时不能用类调用，然而类使用setattr方法，会把那个方法绑定到类的字典上。

思考 
这种动态增加属性的方式和装饰器修饰一个类、Mixin方式的差异？

这种动态增删属性的方式是运行时改变类或者实例的方式，但是装饰器或Mixin都是定义时就决定了，因此反射能力具有更大的灵活性。

### 反射相关的魔术方法

`__getattr__()`、`__setattr__()`、`__delattr__()`这三个魔术方法。

| 魔术方法             | 意义                                                       |
| -------------------- | ---------------------------------------------------------- |
| `__getattr__()`      | 当通过搜索实例、实例的类及祖先类查不到属性，就会调用此方法 |
| `__setattr__()`      | 通过.访问实例属性，进行增加、修改都要调用它                |
| `__delattr__()`      | 当通过实例来删除属性时调用此方法                           |
| `__getattribute__()` | 实例所有的属性调用都从这个方法开始                         |

一个类的属性会按照继承关系找，如果找不到，就会执行` __getattr__()` 方法，如果没有这个方法，就会抛出AttributeError异常表示找不到属性。
查找属性顺序为：  
实例调用`__getattribute__()` -->` instance.__dict__` -->` instance.__class__.__dict__` --> 继承的祖先类（直到object）的`__dict__`---找不到--> 调用`__getattr__()`

```python
class Point(Base):
    z = 6
    def __init__(self, x, y):
        self.x = x
        self.y = y
 
    def show(self):
        print(self.x, self.y)
 
    def __getattr__(self, item):
        return "missing {}".format(item)
 
    def __setattr__(self, key, value):
        print("setattr {}={}".format(key,value))
        self.__dict__[key] = value

```

`__setattr__()`方法，可以拦截对实例属性的增加、修改操作，如果要设置生效，需要自己操作实例的
`__dict__`。

**`__getattribute__`方法**

实例的所有的属性访问，第一个都会调用`__getattribute__`方法，它阻止了属性的查找，该方法应该返回（计算后的）值或者抛出一个AttributeError异常。

- 它的return值将作为属性查找的结果。
- 如果抛出AttributeError异常，则会直接调用`__getattr__`方法，因为表示属性没有找到。

`__getattribute__`方法中为了避免在该方法中无限的递归，它的实现应该永远调用基类的同名方法以访问需要的任何属性，例如`object.__getattribute__(self, name)`。 
注意，除非你明确地知道`__getattribute__`方法用来做什么，否则不要使用它。

## 描述器

描述器的表现
用到3个魔术方法:\_\_get\_\_()、\_\_set\_\_()、\_\_delete\_\_()
方法签名如下 
object.\_\_get\_\_(self, instance, owner) 
object.\_\_set\_\_(self, instance, value)  
object.\_\_delete\_\_(self, instance) 
self 指代当前实例，调用者 
instance 是owner的实例 
owner 是属性的所属的类

### 描述器定义

Python中，一个类实现了`__get__`、`__set__`、`__delete__`三个方法中的任何一个方法，就是描述器。
如果仅实现了`__get__`，就是<font color=red>**非数据描述符 non-data descriptor**</font>； 
同时实现了`__get__`、`__set__`就是<font color=red>**数据描述符 data descriptor**</font>

如果一个类的类属性设置为描述器实例，那么它被称为owner属主。

### 属性查找顺序 

实例的`__dict__` 优先于非数据描述器 
数据描述器优先于实例的`__dict__`
`__delete__`方法有同样的效果，有了这个方法，也是数据描述器。

### Python中的描述器 

------

Python的方法（包括staticmethod()和classmethod()）都实现为非数据描述器。因此，实例可以重新定义和覆盖方法。这允许单个实例获取与同一类的其他实例不同的行为。

property()函数实现为一个数据描述器。因此，实例不能覆盖属性的行为。

```python
class A:
    @classmethod  # 非数据描述器
    def foo(cls): 
        pass
    
    @staticmethod # 非数据描述器
    def bar():
        pass
    
    @property # 数据描述器
    def z(self):
        return 5
    
    def getfoo(self): # 非数据描述器
        return self.foo
    
    def __init__(self): # 非数据描述器
        self.foo = 100
        self.bar = 200
        #self.z = 300
        
a = A()
print(a.__dict__)
print(A.__dict__)


#foo、bar都可以在实例中覆盖，但是z不可以。
```

