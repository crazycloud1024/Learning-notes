---
title: python-标准库、解析式、生成器
date: 2017-08-15
categories: python
tags: python
---
### 标准库

#### datetime模块
- 对日期、时间、时间戳的处理

- datetime类
    - 类方法：

      -  today():返回本地时区当前时间的datetime对象

      -  now(tz=None)：返回当前时间的datetime对象，时间到微秒，如果tz为None，返回和today()一样

      -  utcnow()：没有时区的当前时间（国际化的时候建议用这个）

      -  fromtimestamp(timestamp , tz = None) 从一个时间戳返回一个datetime对象

    - datetime对象

      - timestamp()：返回一个到微秒的时间戳
           - 时间戳：格林威治时间1970年1月1日0点到现在的秒数。
      - 构造方法 datetime.datetime(2016, 12, 6, 16, 29, 43, 79043)
      - year、month、day、hour、minute、second、microsecond，取datetime对象的年月日时分秒及微秒
      - weekday() 返回星期的天，周一0，周日6
      - isoweekday() 返回星期的天，周一1，周日7
      - date() 返回日期date对象
      - time() 返回时间time对象
      - replace() 修改并返回新的时间
      - isocalendar() 返回一个三元组(年，周数，周的天)

    - **日期格式化**

      - 类方法 strptime(date_string, format) ，返回datetime对象

      - 对象方法 strftime(format) ，返回字符串

      - 字符串format函数格式化

        ```python
        import datetime
        dt = datetime.datetime.strptime("21/11/06 16:30", "%d/%m/%y %H:%M")
        print(dt.strftime("%Y-%m-%d %H:%M:%S"))
        print("{0:%Y}/{0:%m}/{0:%d} {0:%H}::{0:%M}::{0:%S}".format(dt))
        ```

- timedelta对象

   - datetime2 = datetime1 + timedelta

    - datetime2 = datetime1 - timedelta

    - timedelta = datetime1 - datetime2

    - 构造方法

      ```python
      - datetime.timedelta(days=0, seconds=0, microseconds=0, milliseconds=0, 
      minutes=0, hours=0, weeks=0) 
      - year = datetime.timedelta(days=365)
      ```
     - total_seconds() 返回时间差的总秒数


#### time模块

- time.sleep(secs) 将调用线程挂起指定的秒数（发起一个系统调用，让程序暂停）

### 解析式

#### 列表解析式

1. 语法:

- [返回值   for   元素   in   可迭代对象   if   条件]

- 使用中括号[]，内部是for循环，if条件语句可选

- 返回一个新的列表

2. 列表解析式是一种语法糖：

- 编译器会优化，不会因为简写而影响效率，反而会因优化而提高了效率。
- 减少了程序员工作量，减少出错。
- 简化了代码，但可读性增强，不便于可读。

3. 举例：

- 获取10以内的偶数
```python
#普通
even = []
for x in range(10):
  if x % 2 == 0:
    even.append(x)
 
#解析式
even = [x for x in range(10) if x%2 == 0]
```
4. 思考:

- 有这样的赋值语句 `newlist = [print(i) for i in range(10)]`打印出来的是什么？
print()把所有的i全打印出来，newlist里面则是10个None

- 获取20以内的偶数，如果同时是3的倍数，把它打印出来
```python
[i for i in range(20) if not i % 2 and i % 3 == 0]
[i for i in range(20) if not i % 2 if i % 3 == 0]
```
- 获取20以内2的倍数或者3的倍数，把它打印出来
`[i for i in range(20) if not i % 2 or i % 3 == 0]`

- "0001.abadicddws" 是ID格式，要求ID格式是以点号分割，左边是4位从1开始的整数，右边是
10位随机小写英文字母。请依次生成前100个ID的列表

```python
import string
import random
x= string.ascii_lowercase
["{:>04}.{}".format(i,"".join(random.choices(x,k = 10))) for i in range(1,8)]

['0001.rvverptnre',
 '0002.beqkpbxhfl',
 '0003.woxvvayzjo',
 '0004.wrbnnkelcc',
 '0005.kpsjowcfvz',
 '0006.pnqwqzlttc',
 '0007.wehobydqnf']
```

#### 字典解析式
1. 语法：

- {返回值 for 元素 in 可迭代对象 if 条件}
- 列表解析式的中括号换成大括号{}就行了
- 使用key:value形式
- 立即返回一个字典
用法：
```python
{x:(x,x+1) for x in range(10)}
{x:[x,x+1] for x in range(10)}
{(x,):[x,x+1] for x in range(10)}
{[x]:[x,x+1] for x in range(10)} #[x]不可哈希，字典的k要求可哈希
{chr(0x41+x):x**2 for x in range(10)}
{str(x):y for x in range(3) for y in range(4)} # 输出多少个元素？
#会覆盖，返回{0：3，1：3，2：3}
```

#### 集合解析式
1. 语法

- {返回值 for 元素 in 可迭代对象 if 条件}
- 列表解析式的中括号换成大括号{}就行了
- 立即返回一个集合
用法
```python
{(x,x+1) for x in range(10)}
{[x] for x in range(10)} #[x]是个集合，不能哈希，所以放在集合解析式里面会报错
```

#### 生成器

##### 生成器Generator

生成器指的是生成器对象，可以由生成器表达式得到，也可以使用yield关键字得到一个生成器函数，调用这个函数得到一个生成器对象。

生成器函数：

1. 函数体内包含yield语句的函数，返回一个生成器对象，生成器函数的函数体不会立即执行。

    生成器对象，是一个可迭代对象，是一个迭代器。

2. 生成器对象，是延迟计算，惰性求值。

3. next(generator)会从函数的当前位置向后执行到之后碰到的第一个yield语句，会弹出值，并暂停函数执行。

4. 再次执行会执行到下一个yield语句，没有多余的yield语句能执行，如果函数没有显式的return语句，继续调用next函数就会抛出StopIteration异常。

5. return会导致无法继续获取下一个值，抛出StopIteration异常。

6. 生成器函数，它是函数，不过这个函数不像普通的函数调用时能返回一个合法的值，它返回的是一个还没有求过任何值的生成器对象，用next拨一下才会往后执行一下。

7. 生成器提供了一个send方法，该方法可以和生成器方向沟通。调用send方法，就可以把send的实参传递给yield语句做结果，这个结果可以在等式右边被赋值给其他变量。yield和next一样可以推动生成器启动并执行。

    ```python
    #举例
    def gen():
        print('line 1')
        yield 1
        print('line 2')
        yield 2
        print('line 3')
        return 3
    next(gen()) # line 1
    next(gen()) # line 1
    g = gen()
    print(next(g)) # line 1
    print(next(g)) # line 2
    print(next(g, 'End')) # 没有元素给个缺省值
    print(next(g, 'End')) # 没有元素给个缺省值
    ```


生成器的应用

```python
#计数器
def inc():
    def counter():
        i = 0
        while True:
            i += 1
            yield i
    c = counter()
    return lambda : next(c)

foo = inc()
print(foo())   #调用的时候是因为inc()函数返回的是一个匿名函数，而匿名函数的调用方式是（lambda 参数列表：函数体)(),所以调用的时候是foo后要加上（），即也就是inc()()。


#等价于
def inc():
    def counter():
        i = 0
        while True:
            i += 1
            yield i
    c = counter()
    
    def _inc():  #用到外面的自由变量（c）：闭包
        return next(c)
    return _inc
foo = inc()	   #可调用对象，函数
print(foo())
```

##### yield from

yield from是python3.3出现的新语法

yield from iterable 是for item in iterable： yield item形式的语法糖。

```python
for x in range(1000):
    yield x
    
#等价于

yield from range(1000)
```

从可迭代对象中一个个拿数据

```python
def counter(n):   #生成器，迭代器
    for x in range(n):
        yield x
        
def inc(n):
    yield from counter(n)
    
foo = inc(10)
print(next(foo))
print(next(foo))
```

##### 协程coroutine

 生成器的高级用法
 比进程、线程轻量级
 是在用户空间调度函数的一种实现
 Python3 asyncio就是协程实现，已经加入到标准库
 Python3.5 使用async、await关键字直接原生支持协程
 协程调度器实现思路

- 有2个生成器A、B
- next(A)后，A执行到了yield语句暂停，然后去执行next(B)，B执行到yield语句也暂停，然后再次调用next(A)，再调用next(B)在，周而复始，就实现了调度的效果
- 可以引入调度的策略来实现切换的方式

 协程是一种非抢占式调度

##### 生成器表达式Generator expression

1. 语法

- (返回值 for 元素 in 可迭代对象 if 条件)

- 列表解析式的中括号换成小括号就行了

- 返回一个生成器

2. 和列表解析式的区别

- 生成器表达式是按需计算（或称惰性求值、延迟计算），需要的时候才计算值，返回迭代器，可以迭代，从前到后走完一遍后，不能回头。

- 列表解析式是立即返回值，返回的不是迭代器，返回可迭代对象列表， 从前到后走完一遍后，可以重新回头迭代。

3. 和列表解析式的对比

- 计算方式
  - 生成器表达式延迟计算，列表解析式立即计算
- 内存占用
  - 单从返回值本身来说，生成器表达式省内存，列表解析式返回新的列表
  - 生成器没有数据，内存占用极少，但是使用的时候，虽然一个个返回数据，但是合起来占用的内存也差不多
  - 列表解析式构造新的列表需要占用内存
- 计算速度
	- 单看计算时间看，生成器表达式耗时非常短，列表解析式耗时长
	- 但是生成器本身并没有返回任何值，只返回了一个生成器对象
	- 列表解析式构造并返回了一个新的列表

4. 生成器

- 可迭代对象(able结尾)
- 迭代器(or结尾)，是不是迭代器用next（）方法可以检测，iter（）方法可以把一个可迭代对象封装成迭代器。
- 生成器和迭代器是不同的对象，但都是可迭代对象，生成器对象，就是迭代器对象，迭代器不一定是生成器。


#### 内建函数
1. 标识 id 


- 返回对象的唯一标识，CPython返回内存地址

2. 哈希 hash() 
- 返回一个对象的哈希值

3. 类型 type() 
- 返回对象的类型

4. 类型转换
- float() int() bin() hex() oct() bool() list() tuple() dict() set() complex() bytes() bytearray() 

5. 输入 input([prompt]) 
- 接收用户输入，返回一个字符串

6. 打印 print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)
- 打印输出，默认使用空格分割、换行结尾，输出到控制台

7. 对象长度 len(s)
- 返回一个集合类型的元素个数

8. isinstance(obj, class_or_tuple) 
- 判断对象obj是否属于某种类型或者元组中列出的某个类型
- isinstance(True, int)

9. issubclass(cls, class_or_tuple)
- 判断类型cls是否是某种类型的子类或元组中列出的某个类型的子类
- issubclass(bool, int)

10. 绝对值abs(x)  x为数值

11. 最大值max() 最小值min() 
- 返回可迭代对象中最大或最小值
- 返回多个参数中最大或最小值

12. round(x) 四舍六入五取偶，round(-0.5)

13. pow(x , y) 等价于 x**y

14. range(stop) 从0开始到stop-1的可迭代对象；range(start, stop[, step])从start开始到stop-1结束步长为step的可迭代对象

15. divmod(x, y) 等价于 tuple (x//y, x%y)

16. sum(iterable[, start]) 对可迭代对象的所有数值元素求和，start：初始值
- sum(range(1,100,2))

17. chr(i) 给一个一定范围的整数返回对应的字符
- chr(97)    chr(20013)

18. ord(c) 返回字符对应的整数
- ord('a')    ord('中')

19. sorted
    sorted(iterable[, key]\[, reverse] )排序
- 返回一个新的列表，对一个可迭代对象的所有元素进行排序，默认升序
- reverse是反转
```python
sorted([1, 3, 5])
sorted([1, 3, 5], reverse=True)   #[5， 3 ， 1]
sorted({'c':1, 'b':2, 'a':1})
```

20. 翻转 reversed(seq)
返回一个翻转元素的迭代器(惰性求值)
```python
list(reversed("13579"))
{ reversed((2, 4)) } # 有几个元素？
for x in reversed(['c','b','a']):
	print(x)
reversed(sorted({1, 5, 9}))
```

21. 枚举 enumerate(seq, start=0)

迭代一个序列，返回索引数字和元素构成的二元组
start表示索引开始的数字，默认是0
```python
for x in enumerate([2,4,6,8]):
	print(x)
for x in enumerate("abcde"):
	print(x,end=" ")
```
22. 迭代器和取元素 iter(iterable)、next(iterator[, default])

iter将一个可迭代对象封装成一个迭代器
next对一个迭代器取下一个元素。如果全部元素都取过了，再次next会抛StopIteration异常
```python
    it = iter(range(5))
    next(it)
    
    it = reversed([1,3,5])
    next(it)
```

23. 拉链函数zip(*iterables) 
像拉链一样，把多个可迭代对象合并在一起，返回一个迭代器
将每次从不同对象中取到的元素合并成一个元组
```python
list(zip(range(10),range(10)))
list(zip(range(10),range(10),range(5),range(10)))

dict(zip(range(10),range(10)))
{str(x):y for x,y in zip(range(10),range(10))}
```
24. 过滤数据filter(function, iterable) --- >filter object

    过滤可迭代对象的元素，返回一个迭代器

    function是一个具有一个参数的函数。返回bool

    ```python
    #例子：过滤出数列中能被3整除的数字
    In [4]: list(filter(lambda x: x%3 == 0,[1,9,32,3,21,0,-1,123]))
    Out[4]: [9, 3, 21, 0, 123]
    ```

25. reduce：连续计算，连续从一个可迭代对象中获取值，来进行计算，需要从functools模块中导入

26. 映射map(func,  *iterables) ---->    map object

    对多个可迭代对象的元素按照指定的函数进行映射，返回一个迭代器