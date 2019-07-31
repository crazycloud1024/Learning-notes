---
title: python-面向对象
date: 2017-10-01
categories: python
tags: python
---
### 面向对象

------

一种认识世界，分析世界的方法论。将万事万物抽象为类

类class

类是抽象的概念，是万事万物的抽象，是一类事物的共同特征的集合。用计算机语言来描述就是**属性**和**方法**的集合（封装）。

对象instance、object，对象是类的具象，是一个实体，类也是对象。

属性，它是对象状态的抽象，用数据结构来描述。

操作，是对对象行为的抽象，用操作名和实现该操作的方法来描述。

**哲学**

一切皆对象

对象是数据和操作的封装

对象是独立的，但是对象之间可以相互作用

目前OOP是最接近人类认知的编程范式

#### 面向对象三要素

<font color = red>**封装**</font>：将属性和方法组装到一起，隐藏数据，对外只暴露一些接口用于连接。

<font color = red>**继承**</font>：目的：多复用，继承来的就不用自己写了；多继承少修改（开闭原则），OCP(open_closed principle)，使用继承来改变。作用：就是为了修改不一样的属性。

<font color = red>**多态**</font>：面向对象编程最灵活的地方，多种表现，动态绑定。

#### Python的类

定义：

```python
class ClassName：
	语句块
```

**要求：**

1. 必须使用class关键字

2. 类名必须使用大驼峰命名

3. 类定义完成后会产生一个类对象，会绑定到ClassName这个标识符上面。

#### 类对象和类属性

<font color = red>**类对象**</font>，类的定义就会生成一个类对象

<font color = red>**类的属性**</font>，类定义中的变量和类中定义的方法都是类的属性

<font color = red>**类变量**</font>，定义在类中的变量

#### 实例化

在类对象名称后面加上一个括号，就调用类的实例化方法，完成实例化，实例化就真正创建一个该类的对象（实例）。

每一次实例化，只能生成该类的一个具体实例，生成的是不同的实例。即使通过相同的参数实例化，得到的对象也不相同。

python类实例化后，会自动调用`__init__`方法，可以不定义，如果没有定义会在实例化后**隐式**调用，初始化函数可以有多个参数，第一参数必须留给self，init方法不能有返回值，也就是只能有return None

作用：对实例进行<font color = red>**初始化**</font>

类里面的函数叫做方法对象method，不是普通的函数对象function，一般至少要有一个参数，第一个参数可以是self（一般习惯上用self），这个参数位置指代的就是当前这个实例本身。

#### 实例对象instance

类实例化后一定会获得一个对象，就是<font color = red>**实例对象**</font>。init方法的第一个参数self就是指代某一个实例。

#### 实例变量和类变量

实例变量是每个实例自己的变量，是自己独有的；列变量是类的变量，是类的所有实例共享的属性和方法。

| 特殊属性      | 含义             |
| ------------- | ---------------- |
| \__name__     | 对象名           |
| \__class__    | 对象的类型       |
| \__dict__     | 对象的属性的字典 |
| \__qualname__ | 类的限定名       |

类属性保存在类的dict中，实例属性保存在实例的dict中，如果从实例访问类的属性，就要借助class找到所属的类。

类有类名字，实例没有实体名。

python中每一种对象都拥有不同的属性。函数、类都是对象，类的实例也是对象。

类不可以访问实例的属性，实例可以访问类的属性。（是类的，也是这个类所有实例的，其实例都可以访问到；是实例的，就是这个实例自己的，通过类访问不到。）

对象（实例或类）可以动态的给自己增加一个属性，实例.\__dict__[变量名]可以访问到，实例的同名变量会隐藏掉类变量，或者说是覆盖了这个类的变量。

一般来说，属性使用字典保存是为了提升查找效率，必须用空间换时间，但是也有个问题，如果数百万个对象，那么字典占的比较大，所以可以使用\_\_slots\_\_方法。

slots告诉解释器，实例的属性都叫什么，一般来说，既然要节省内存，那就最好还是使用元组。

一旦类提供了slots，就阻止实例产生dict来保存实例的属性。子类不会继承slots

```python
class A:
    X = 1

    __slots__ = ('z','y')

    def __init__(self):
        self.y = 5

    def show(self):
        print(self.X,self.y)

a = A()
a.show()

print("A",A.__dict__.keys())
# print("obj",a.__dict__.keys())
```

<font color = red>**实例属性的查找顺序 **</font>
指的是实例使用.点号来访问属性，会先找实例自己的\__dict\_\_，如果没有，然后通过属性\_\_class\_\_找到自己的类，再去类的\_\_dict\_\_中找类属性中没找到就往类继承的父类中去查找，最终找到根基类object，没找到则抛出异常 。
注意，如果实例使用\_\_dict\_\_[变量名]访问变量，将不会按照上面的查找顺序找变量了，这是指明使用字典的key查找，不是属性查找。
一般来说，类变量可使用全大写来命名。

#### 装饰一个类

为一个类通过装饰，添加一些类属性。

```python
#增加类变量
def adda_name(name,cls):
    cls.NAME = name  #动态增加类属性
#改进成装饰器
def add_name(name):
    def wrapper(cls):
        cls.NAME = name
        return cls
    return wrapper
@add_name("jerry")
class Person():  #Person =  add_name("jerry")(Person) = wrapper(person) =  Person
    AGE = 3

# adda_name(Person ,"tom")
	
print(Person.__dict__)
print(Person.NAME)
```

之所以能够装饰，本质上是为类对象动态的添加了一个属性，而Person这个标识符指向这个类对象。

------

#### 类方法和静态方法

类方法

```python
class Person:
    @classmethod
    def class_method(cls): # cls是什么
        print('class = {0.__name__} ({0})'.format(cls))
        cls.HEIGHT = 170
 
Person.class_method()
print(Person.__dict__)
```
类方法
1. 在类定义中，使用@classmethod装饰器修饰的方法，不管使用实例还是类来调用，调用的都是类的方法，传入当前类自身。
2. 必须至少有一个参数，且第一个参数留给了cls，cls指代调用者即<font color = red>**类对象自身**</font>
3. cls这个标识符可以是任意合法名称，但是为了易读，请不要修改

4. 通过cls可以直接操作类的属性

静态方法

```python
class Person:
    def method(self,name)： #普通方法
    	print(self)
   
    @classmethod   #类方法
    def class_method(cls ， age):  # cls是什么  是当前类
        print('class = {0.__name__} ({0})'.format(cls))
        cls.HEIGHT = 170

    @staticmethod  #静态方法
    def static_methd(a):
        print(Person.HEIGHT , a)

        
Person.method(Person(),"ken")  #第一参数不会自动传入，自己传Person（）
Person().method("tom")  #第一参数自动传入Person（）

Person.class_method(20)  #第一参数cls自动传入
Person().method(18)  #第一参数cls自动传入

Person.static_methd()   #第一参数不用传
Person().static_method()  #第一参数不用传

print(Person.__dict__)
```

静态方法
1. 在类定义中，使用@staticmethod装饰器修饰的方法
2. 调用时，不管是实例还是类调用，不会隐式的传入参数 。
    静态方法，只是表明这个方法属于这个名词空间。函数归在一起，方便组织管理。

总结： 
类除了普通方法都可以调用，普通方法需要对象的实例作为第一参数。 
实例可以调用所有类中定义的方法（包括类方法、静态方法），普通方法传入实例自身，静态方法和类方法需要找到实例的类。

实例方法

如果用实例，第一参数则传入self，则叫做绑定。如果使用类调用，则没有绑定（未绑定行为）。

#### 访问控制

##### 私有(Private)属性

使用双下划线开头的属性名，就是私有属性
```python
class Person:
    def __init__(self, name, age=18):
        self.name = name
        self.__age = age
 
    def growup(self, i=1):
        if i > 0 and i < 150: # 控制逻辑
            self.__age += i
 
    def getage(self):
        return self.__age
 
print(Person('tom').getage())

```
私有变量的本质： 
类定义的时候，如果声明一个实例变量的时候，使用双下划线，Python解释器会将其改名，转换名称为**\_类名\_\_变量名**的名称，所以用原来的名字访问不到了。

##### 保护变量

在变量名前使用一个下划线，称为保护变量。

```python
class Person:
    def __init__(self, name, age=18):
        self.name = name
        self._age = age
 
tom = Person('Tom')
print(tom._age)
print(tom.__dict__)
```
可以看出，这个\_age属性根本就没有改变名称，和普通的属性一样，解释器不做任何特殊处理。 这只是开发者共同的约定，看见这种变量，就如同私有变量，不要直接使用。

##### 私有方法
参照保护变量、私有变量，使用单下划线、双下划线命名方法。

私有方法的本质 

单下划线的方法只是开发者之间的约定，解释器不做任何改变。 

双下划线的方法，是私有方法，解释器会改名，改名策略和私有变量相同，**\_类名\_\_方法名 **。 
方法变量都在类的**\_\_dict\_\_**中可以找到。

私有成员的总结 :

在Python中使用_单下划线或者 \_\_ 双下划线来标识一个成员被保护或者被私有化隐藏起来。 
但是，不管使用什么样的访问控制，都不能真正的阻止用户修改类的成员。Python中没有绝对的安全的保护成员或者私有成员。 
因此，前导的下划线只是一种警告或者提醒，请遵守这个约定。除非真有必要，不要修改或者使用保护成员或者私有成员，更不要修改它们。

```python
class Person:
    def __init__(self, name, age=18):
        self.name = name
        self._age = age
 
    def _getname(self):
        return self.name
 
    def __getage(self):
        return self._age
 
tom = Person('Tom')
print(tom._getname()) # 没改名
print(tom.__getage()) # 无此属性
print(tom.__dict__)
print(tom.__class__.__dict__)
print(tom._Person__getage()) # 改名了
```

#### 补丁 

可以通过修改或者替换类的成员。使用者调用的方式没有改变，但是，类提供的功能可能已经改变了。

猴子补丁（Monkey Patch）： 
在运行时，对属性、方法、函数等进行动态替换。 
其目的往往是为了通过替换、修改来增强、扩展原有代码的能力。 
黑魔法，慎用。

```python
# test1.py
from test2 import Person
from test3 import get_score
 
def monkeypatch4Person():
    Person.get_score = get_score
 
monkeypatch4Person() # 打补丁
 
if __name__ == "__main__":
    print(Person().get_score())
```
```python
# test2.py
class Person:
    def get_score(self):
        # connect to mysql
        ret = {'English':78, 'Chinese':86, 'History':82}
        return ret
```
```python
# test3.py
def get_score(self):
    return dict(name=self.__class__.__name__,English=88, Chinese=90, History=85)
```

上例中，假设Person类get_score方法是从数据库拿数据，但是测试的时候，不方便。 
为了测试时方便，使用猴子补丁，替换了get_score方法，返回模拟的数据。

#### 属性装饰器 

一般好的设计是：把实例的属性保护起来，不让外部直接访问，外部使用getter读取属性和setter方法设置属性。

```python
class Person:
    def __init__(self, name, age=18):
        self.name = name
        self.__age = age

    @property
    def age(self):
        return self.__age

    @age.setter
    def age(self, age):
        self.__age = age

    @age.deleter
    def age(self):
        # del self.__age
        print('del')

tom = Person('Tom')
print(tom.age)
tom.age = 20
print(tom.age)
del tom.age
```

特别注意：使用property装饰器的时候这三个方法**同名**
property装饰器 
后面跟的函数名就是以后的属性名。它就是getter。这个必须有，有了它至少是只读属性

setter装饰器 
与属性名同名，且接收2个参数，第一个是self，第二个是将要赋值的值。有了它，属性可写

deleter装饰器 
可以控制是否删除属性。很少用

property装饰器必须在前，setter、deleter装饰器在后。 
property装饰器能通过简单的方式，把对方法的操作变成对属性的访问，并起到了一定隐藏效果.

#### 对象的销毁 

类中可以定义 \_\_del\_\_ 方法，称为析构函数（方法）。

作用：销毁类的实例的时候调用，以释放占用的资源。其中就放些清理资源的代码，比如释放连接。

注意这个方法不能引起对象的真正销毁，只是对象销毁的时候会自动调用它。

使用del语句删除实例，引用计数减1。当引用计数为0时，会自动调用\_\_del\_\_ 方法。 
由于Python实现了垃圾回收机制，不能确定对象何时执行垃圾回收。

由于垃圾回收对象销毁时，才会真正清理对象，还会在回收对象之前自动调用\_\_del\_\_ 方法，除非你明确知道自己的目的，建议不要手动调用这个方法。

#### 方法重载(overload) 

其他面向对象的高级语言中，会有重载的概念。 
所谓重载，就是同一个方法名，但是参数数量、类型不一样，就是同一个方法的重载。

Python没有重载！ 
Python不需要重载！ 
Python中，方法（函数）定义中，形参非常灵活，不需要指定类型（就算指定了也只是一个说明而非约束），参数个数也不固定（可变参数）。一个函数的定义可以实现很多种不同形式实参的调用。所以Python不需要方法的重载。

或者说Python本身就实现了其它语言的重载。

#### 封装 

面向对象的三要素之一，封装Encapsulation

将数据和操作组织到类中，即属性和方法 

将数据隐藏起来，给使用者提供操作（方法）。使用者通过操作就可以获取或者修改数据。getter和setter。 

通过访问控制，暴露适当的数据和操作给用户，该隐藏的隐藏起来，例如保护成员或私有成员。