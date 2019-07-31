---
title: python-文件处理、路径处理、序列化和反序列化
date: 2017-09-10
categories: python
tags: python
---
#### 文件IO常用操作

一般说IO操作，指的是文件IO。

把文件存储到磁盘上的这个过程，叫做落地。

|  column   |    column    |
| :-------: | :----------: |
|   open    |     打开     |
|   read    |     读取     |
|   write   |     写入     |
|   close   |     关闭     |
| readline  |    行读取    |
| readlines |   多行读取   |
|   seek    | 文件指针操作 |
|   tell    |   指针位置   |

##### open打开操作

```python
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True,opener=None)

f = open("file名字")   #文件对象
print(f.read())		#读取文件
f.close()		#关闭文件
```

打开一个文件，返回一个文件对象(流对象)和文件描述符。打开文件失败，则返回异常。
基本使用： 创建一个文件test，然后打开它，用完关闭。

文件操作中最常用的就是读和写。文件访问的模式有两种：文本模式和二进制模式。

> 注：windows中使用codepage代码页。可以认为每一个代码页就是一张编码表 cp936和gbk等价。

##### mode模式

| 描述字符 |                                                    |
| -------- | -------------------------------------------------- |
| r        | 缺省的，表示只读打开                               |
| w        | 只写打开，有的话就清除重新写                       |
| x        | 创建并写入一个新文件                               |
| a        | 写入打开，如果文件存在，则追加                     |
| b        | 二进制模式                                         |
| t        | 缺省的，文本模式                                   |
| +        | 读写打开一个文件，给原来只读、只写的增加缺失的功能 |

open默认是只读模式r打开已经存在的文件。

- r 只读打开文件，如果使用write方法，会抛异常。 如果文件不存在，抛出FileNotFoundError异常。

- w 表示只写方式打开，如果读取则抛出异常 如果文件不存在，则直接创建文件 如果文件存在，则清空文件内容。

- x 文件不存在，创建文件，并只写方式打开，文件存在，抛出FileExistsError异常。

- a 文件存在，只写打开，追加内容 文件不存在，则创建后，只写打开，追加内容

- r是只读，wxa都是只写。 wxa都可以产生新文件，w不管文件存在与否，都会生成全新内容的文件；a不管文件是否存在，都能在打开的文件尾部追加；x必须要求文件事先不存在，自己造一个新文件。

- +为r、w、a、x提供缺失的读或写功能，但是，获取文件对象依旧按照r、w、a、x自己的特征。 +不能单独使用，可以认为它是为前面的模式字符做增强功能的。

t和b：

- 文本模式t 字符流，将文件的字节按照某种字符编码理解，按照字符操作。open的默认mode就是rt。

- 二进制模式b 字节流，将文件就按照字节理解，与字符编码无关。二进制模式操作时，字节操作使用bytes类型。

t/b不能单独存在，要和a/w/x/r配合使用。

##### seek文件指针

文件指针，指向当前字节位置。

mode = r，指针起始在0  ，mode  =  a  指针起始在EOF。

tell（）：显示指针当前位置。

seek(offset[, whence]) 移动文件指针位置。offest偏移多少字节，whence从哪里开始。

- 文本模式下 whence 0 缺省值，表示从头开始，offset只能正整数 whence 1 表示从当前位置，offset只接受0，whence 2 表示从EOF开始，offest只接受0

  ```python
  # 文本模式
  f = open('test4','r+')
  f.tell() # 起始
  f.read()
  f.tell() # EOF
  f.seek(0) # 起始
  f.read()
  f.seek(2,0)
  f.read()
  f.seek(2,0)
  f.seek(2,1) # offset必须为0
  f.seek(2,2) # offset必须为0
  f.close()
  ```

- 二进制模式下 whence 0 缺省值，表示从头开始，offest只能正整数 whence 1表示从当前位置，offest可正可负，whence 2 表示从EOF开始，offest可正可负。

  ```python
  # 二进制模式
  f = open('test4','rb+')
  f.tell() # 起始
  f.read()
  f.tell() # EOF
  f.write(b'abc')
  f.seek(0) # 起始
  f.seek(2,1) # 从当前指针开始，向后2
  f.read()
   
  f.seek(-2,2) # 从EOF开始，向前2
  f.read()
  f.seek(-20,2) # OSError
  f.close()
  ```

- 二进制模式支持任意起点的偏移，从头、从尾、从中间位置开始。 向后seek可以超界，但是向前seek的时候，不能超界，否则抛异常。

##### buffering缓冲区

-1 表示使用缺省大小的buffer。如果是二进制模式，使io.DEFAULT_BUFFER_SIZE值，默认是<font color= red>4096或者8192</font>。如果是文本模式，如果是终端设备，是行缓存方式，如果不是，则使用二进制模式的策略。

- 0 只在二进制模式使用，表示关buffer
- 1 只在文本模式使用，表示使用行缓冲。意思就是见到换行符就flush
- 大于1 用于指定buffer的大小

buffer 缓冲区 

缓冲区一个内存空间，一般来说是一个FIFO队列，到缓冲区满了或者达到阈值，数据才会flush到磁盘。

flush() 将缓冲区数据写入磁盘 close() 关闭前会调用flush()。

io.DEFAULT_BUFFER_SIZE 缺省缓冲区大小，字节。

| buffering      | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| buffering = -1 | t和b，都是io.DEFAULT_BUFFER_SIZE                             |
| buffering = 0  | b 关闭缓冲区 <br/>t 不支持                                   |
| buffering = 1  | b 就一个字节<br/>t 行缓冲，遇到换行符才flush                 |
| buffering > 1  | b模式表示行缓冲大小。缓冲区的值可以超过io.DEFAULT_BUFFER_SIZE，直到设定的值超出后才把缓冲区flush 。
t模式，是io.DEFAULT_BUFFER_SIZE字节，flush完后把当前字符串也写入磁盘 |

一般来说：

1. 文本模式，一般都用默认缓冲区大小
2. 二进制模式，是一个个字节的操作，可以指定buffer的大小
3. 一般来说，默认缓冲区大小是个比较好的选择，除非明确知道，否则不调整它
4. 一般编程中，明确知道需要写磁盘了，都会手动调用一次flush，而不是等到自动flush或者close的时候

##### 其他参数

编码：windows下缺省GBK（0xB0A1），Linux下缺省UTF-8（0xE5 95 8A）

errors ：编码错误将被捕获 None和strict表示有编码错误将抛出ValueError异常；ignore表示忽略

newline：文本模式中，换行的转换。可以为None、'' 空串、'\r'、'\n'、'\r\n' 。

- None表示'\r'、'\n'、'\r\n'都被转换为'\n'；
- ''  表示不会自动转换通用换行符；其它合法字符表示换行符就是指定字符，就会按照指定字符分行写。
- '\n'或''表示'\n'不替换；其它合法字符表示'\n'会被替换为指定的字符

closefd：关闭文件描述符，True表示关闭它。False会在文件关闭后保持这个描述符。fileobj.fileno()查看。

**文件描述符**：Linux一切皆文件，文件打开后都会有一个位于的文件描述符，在计算机系统中是一个有限的资源。0,1,2,标准输入，标准输出，标准错误输出。

对于类似于文件对象的IO对象，一般来说都需要在不使用的时候关闭、注销，以释放资源。 
IO被打开的时候，会获得一个文件描述符。计算机资源是有限的，所以操作系统都会做限制。就是为了保护计算机的资源不要被完全耗尽，计算资源是共享的，不是独占的。 

一般情况下，除非特别明确的知道资源情况，否则不要提高资源的限制值来解决问题。

##### read（）

read(size=-1) 
size表示读取的多少个字符或字节；负数或者None表示读取到EOF

readline(size=-1) 
一行行读取文件内容。size设置一次能读取行内几个字符或字节。

readlines(hint=-1) 
读取所有行的列表。指定hint则返回指定的行数。

##### write（）

write(s)，把字符串s写入到文件中并返回字符的个数 writelines(lines)，将字符串列表写入文件。

##### close（）

flush并关闭文件对象。 
文件已经关闭，再次关闭没有任何效果。

##### 其他

| 名称          | 说明         |
| ------------- | ------------ |
| seekable（）  | 是否可seek   |
| readable（）  | 是否可读     |
| writeable（） | 是否可写     |
| closed（）    | 是否已经关闭 |

##### 上下文管理

1、异常处理
当出现异常的时候，拦截异常。但是，因为很多代码都可能出现OSError异常，还不好判断异常就是应为资源限制产生的。

```python
f = open('test')
try:
    f.write("abc") # 文件只读，写入失败
finally:
    f.close() # 这样才行
```

使用finally可以保证打开的文件可以被关闭。

上下文管理
1. 使用with ... as 关键字

2. 上下文管理的语句块并不会开启新的作用域

3. with语句块执行完的时候，会自动关闭文件对象

#### StringIO操作

io模块中的类

	from io import StringIO

内存中，开辟的一个文本模式的buffer，可以像文件对象一样操作它

当close方法被调用的时候，这个buffer会被释放

```python
from io import StringIO
# 内存中构建
sio = StringIO() # 像文件对象一样操作
print(sio.readable(), sio.writable(), sio.seekable())# True True True
sio.write("luo\nPython")
sio.seek(0)  
print(sio.readline())  #magedu
print(sio.getvalue()) # 无视指针，输出全部内容  magedu   Python
sio.close()
```

好处

	一般来说，磁盘的操作比内存的操作要慢得多，内存足够的情况下，一般的思路是少落地，减少磁盘IO的过程，可以大大的提高程序的运行效率。

#### BytesIO操作

io模块中的类

	from io import BytesIO

内存中，开辟的一个二进制模式的buffer，可以像文件对象一样操作它

当close方法被调用的时候，这个buffer会被释放

```python
from io import BytesIO # 内存中构建
bio = BytesIO()
print(bio.readable(), bio.writable(), bio.seekable()) #True True True
bio.write(b"luo\nPython")
bio.seek(0)
print(bio.readline())  # b'magedu\n'
print(bio.getvalue()) # 无视指针，输出全部内容   b'magedu\nPython'
bio.close()
```

#### file-like对象

类文件对象，可以像文件对象一样操作。

socket对象，输入输出对象（stdin、stdout）都是类文件对象

```python
from sys import stdout, stderr
f = stdout
print(type(f))  #<class 'ipykernel.iostream.OutStream'>
f.write('magedu.com')  #magedu.com
```

#### 路径操作

##### os.path模块

3.4版本之前

```python
from os import path
p = path.join('d:/','tmp')
print(type(p), p)               #<class 'str'> d:/tmp
print(path.exists(p))     #判断是否存在该路径  True
print(path.split(p)) # (head,tail)     ('d:/', 'tmp')
print(path.abspath('.'))   # 打印当前的绝对路径      C:\Users\vampire\python
p = path.join('D:/', p, 'test.txt')   #  'd:/tmp\\test.txt'
print(path.dirname(p)) # 目录名
print(path.basename(p)) #基名，就是文件名
print(path.splitdrive(p)) #二元组  ('d:', '/tmp\\test.txt'）

```

```python
p1 = path.abspath(".")  #“文件路径”
print(p1, path.basename(p1))
while p1 != path.dirname(p1):
    p1 = path.dirname(p1)
    print(p1, path.basename(p1))

​```
C:\Users\vampire\python python
C:\Users\vampire vampire
C:\Users Users
C:\ 
​```
```

##### pathlib模块

提供Path对象来操作。包括目录和文件。

导入模块：from pathlib import Path

**目录操作初始化**：

```python
p = Path() # 当前目录    WindowsPath('.')
p.absolute()#   WindowsPath('C:/Users/vampire/python')
p = Path('a','b','c/d') # 当前目录下的 WindowsPath('C:/Users/vampire/python/a/b/c/d')
p = Path('/etc') # 根下的etc目录
```

**路径拼接和分解**

###### 操作符/

Path对象 / Path对象 
Path对象 / 字符串 或者 字符串 / Path对象 

###### 分解 

parts属性，可以返回路径中的每一个部分 

```python
p3.absolute()   #WindowsPath('C:/Users/vampire/python/c/a')
p3.absolute().parts   #('C:\\', 'Users', 'vampire', 'python', 'c', 'a')
```

###### joinpath 

joinpath(*other) 连接多个字符串到Path对象中

```python
p = Path()   #   WindowsPath('.')
p = p / 'a'   #  WindowsPath('a')
p.absolute()  #   WindowsPath('C:/Users/vampire/python/a')
p1 = 'b' / p  #   WindowsPath('C:/Users/vampire/python/b/a')
p2 = Path('c') #  WindowsPath('C:/Users/vampire/python/c')
p2.absolute()  #   WindowsPath('C:/Users/vampire/python/c')
p3 = p2 / p1  #  WindowsPath('c/b/a')
p3.absolute()  #  WindowsPath('C:/Users/vampire/python/c/b/a')
print(p3.parts)   #
p3.absolute().parts # ('C:\\', 'Users', 'vampire', 'python', 'c', 'b', 'a')
p3.joinpath('etc','init.d',Path('httpd'))
```

###### 获取路径

str 获取路径字符串 

bytes 获取路径字符串的bytes 

```python
p = Path('/etc')
print(str(p), bytes(p))

#  \etc   b'\\etc'
```

###### 父目录

parent 目录的逻辑父目录 

parents 父目录序列，索引0是直接的父 

```python
p = Path('/a/b/c/d')
print(p.absolute())   #C:\a\b\c\d
print(p.parent.parent)  #\a\b
for x in p.parents:
    print(x)
    
#\a\b\c
#\a\b
#\a
#\
```

###### 目录的组合部分

name、stem、suffix、suffixes、with_suffix(suffix)、with_name(name) 

name 目录的最后一个部分 

suffix 目录中最后一个部分的扩展名 

stem 目录最后一个部分，没有后缀 

suffixes 返回多个扩展名列表 

with_suffix(suffix) 有扩展名则替换，无则补充扩展名 

with_name(name) 替换目录最后一个部分并返回一个新的路径

```python
p = Path('mysqlinstall/mysql.tar.gz')
print(p.name)   #mysql.tar.gz
print(p.suffix)  #.gz
print(p.suffixes)  # ['.tar', '.gz']
print(p.stem)   # mysql.tar
print(p.with_name('mysql-5.tgz'))   #\mysqlinstall\mysql-5.tgz
print(p.with_suffix('.png'))   #\mysqlinstall\mysql.tar.png
p = Path('README')  #  README
print(p.with_suffix('.txt'))   #  README.txt
```

###### 判断方法

`is_dir()` 是否是目录，目录存在返回True 

`is_file()` 是否是普通文件，文件存在返回True 

`is_symlink()` 是否是软链接 

`is_socket() `是否是socket文件 

`is_block_device() `是否是块设备 

`is_char_device()` 是否是字符设备 

`is_absolute() `是否是绝对路径

`resolve() `返回一个新的路径，这个新路径就是当前Path对象的绝对路径，如果是软链接则直接被解析 

`absolute()` 获取绝对路径

`exists() `目录或文件是否存在 

`rmdir() `删除空目录。没有提供判断目录为空的方法 

`touch(mode=0o666, exist_ok=True)` 创建一个文件 

`as_uri()` 将路径返回成URI，例如'file:///etc/passwd'

`mkdir(mode=0o777, parents=False, exist_ok=False) `

- parents，是否创建父目录，True等同于mkdir -p；False时，父目录不存在，则抛出FileNotFoundError 

- exist_ok参数，在3.5版本加入。False时，路径存在，抛出FileExistsError；True时，FileExistsError被忽略

`iterdir() `迭代当前目录

###### 匹配

match（pattern）

模式匹配，成功返回True。

```python
Path('a/b.py').match('*.py') # True
Path('/a/b/c.py').match('b/*.py') # True
Path('/a/b/c.py').match('a/*.py') # False
Path('/a/b/c.py').match('a/*/*.py') # True
Path('/a/b/c.py').match('a/**/*.py') # True
Path('/a/b/c.py').match('**/*.py') # True
```

stat()  相当于stat命令  ，lstat() 同stat()，但如果是符号链接，则显示符号链接本身的文件信息

###### pathlib模块下的文件操作

`Path.open(mode='r', buffering=-1, encoding=None, errors=None, newline=None)`

使用的方法类似内建函数open，返回一个文件对象。

3.5增加的新函数 

`Path.read_bytes()  `

以'rb'读取路径对应文件，并返回二进制流。看源码

`Path.read_text(encoding=None, errors=None) `

以'rt'方式读取路径对应文件，返回文本。

`Path.write_bytes(data)  `

以'wb'方式写入数据到路径对应文件。

`Path.write_text(data, encoding=None, errors=None) `

以'wt'方式写入字符串到路径对应文件。

```python
p = Path('my_binary_file')
p.write_bytes(b'Binary file contents')
p.read_bytes() # b'Binary file contents'
 
p = Path('my_text_file')
p.write_text('Text file contents')
p.read_text() # 'Text file contents'
 
 
from pathlib import Path
p = Path('o:/test.py')
p.write_text('hello python')
print(p.read_text())
with p.open() as f:
    print(f.read(5))
```

#### csv文件

逗号分隔值Comma-Separated Values。 

CSV 是一个被行分隔符、列分隔符划分成行和列的文本文件。 

CSV 不指定字符编码。

行分隔符为\r\n，最后一行可以没有换行符 

列分隔符常为逗号或者制表符。 

每一行称为一条记录record 

字段可以使用双引号括起来，也可以不使用。如果字段中出现了双引号、逗号、换行符必须使用双引号括起来。如果字段的值是双引号，使用两个双引号表示一个转义。

表头可选，和字段列对齐就行了。

手动生成csv文件

```python
from pathlib import Path

p = Path('D:/tmp/test.csv')
parent = p.parent
if not parent.exists():
    parent.mkdir(parents=True,exist_ok =True) #exist_ok 用在python3.5之后，如果文件目录存在，True则压制异常。
csv_body = '''\
id,name,age,comment
1,zs,18,"I'm 18"
2,ls,20,"this is a ""test"" string."
3,ww,23,"你好

计算机
"
'''
p.write_text(csv_body)

```

##### csv模块

```python
def reader(iterable, dialect='excel', *args, **kwargs)
```

返回一个reader对象，是一个行迭代器

默认使用excel方言，如下：

- delimiter 列分隔符,逗号

- lineterminator 行分隔符\r\n

- quotechar 字段的引用符号，缺省为"双引号

- 双引号的处理

  - doublequote 双引号的处理，默认为True。如果碰到数据中有双引号，而quotechar也是双引号，True则使用2个双引号表示，False表示使用转义字符将作为双引号的前缀。
  - escapechar 一个转义字符，默认为None
  - writer = csv.writer(f, doublequote=False, escapechar='@') 遇到双引号，则必须提供转义字符

- quoting 指定双引号的规则

  - QUOTE_ALL 所有字段

  - QUOTE_MINIMAL特殊字符字段，Excel方言使用该规则

  - QUOTE_NONNUMERIC非数字字段

  - QUOTE_NONE都不使用引号。

```python
def writer(fileobj, dialect='excel', *args, **kwargs)
```

返回DictWriter实例，主要的方法有writerow，writerows。

```python
import csv
 
p = Path('d://tmp/tesr.csv')
with open(str(p)) as f:
    reader = csv.reader(f)    #返回一个迭代对象
    print(next(reader))     #不回头
    print(next(reader))     
    for line in reader:
        print(line)
 
rows = [
    [4,'tom',22,'tom'],
    (5,'jerry',24,'jerry'),
    (6,'justin',22,'just\t"in'),
    "abcdefghi",
    ((1,),(2,))
]
row = rows[0]
 
with open(str(p), 'a',newline="") as f:  #newline为了不换行
    writer = csv.writer(f)
    writer.writerow(row)   #一次写一条
    writer.writerows(rows)    #将所有的一次写入
```

#### ini文件

一般作为配置文件。

ini文件：

```ini
[DEFAULT]
a = test
 
[mysql]
default-character-set=utf8
 
[mysqld]
datadir =/dbserver/data
port = 33060
character-set-server=utf8
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
```

中括号里面的部分称为section，译作节、区、段。 

每一个section内，都是key=value形成的键值对，key称为option选项。

这里的DEFAULT是缺省section的名字，必须大写。

##### configparser模块

configparser模块的ConfigParser类就是用来操作。

可以将section当做key，section存储着键值对组成的字典，可以把ini配置文件当做一个嵌套的字典。默认使用的是有序字典。

```python
read(filenames, encoding=None) 
#读取ini文件，可以是单个文件，也可以是文件列表。可以指定文件编码。
sections()  #返回section列表。缺省section不包括在内。 
add_section(section_name)   #增加一个section。 

has_section(section_name)   #判断section是否存在 
options(section)   #返回section的所有option，会追加缺省section的option 

has_option(section, option)  #判断section是否存在这个option
get(section, option, *, raw=False, vars=None[, fallback]) 

#从指定的段的选项上取值，如果找到返回，如果没有找到就去找DEFAULT段有没有。

getint(section, option, *, raw=False, vars=None[, fallback]) 
getfloat(section, option, *, raw=False, vars=None[, fallback]) 
getboolean(section, option, *, raw=False, vars=None[, fallback]) 
#上面3个方法和get一样，返回指定类型数据。

items(raw=False, vars=None) 
items(section, raw=False, vars=None) 
#没有section，则返回所有section名字及其对象；如果指定section，则返回这个指定的section的键值对组成二元组。

set(section, option, value) 
#section存在的情况下，写入option=value，要求option、value必须是字符串。

remove_section(section) 
#移除section及其所有option 

remove_option(section, option) 
#移除section下的option。

write(fileobject, space_around_delimiters=True) 
#将当前config的所有内容写入fileobject中，一般open函数使用w模式。
```
代码示例：
```python
from configparser import ConfigParser
from pathlib import Path

filename = Path("d://tmp/mysql.ini")
newfilename = Path("d://tmp/mysql111.ini")

cfg = ConfigParser()
read_ok = cfg.read(str(filename))
print(read_ok)
print(cfg.sections())
print(cfg.has_section("mysql"))
print("-"*30)

for k,v in cfg.items():   #未指定section
    print(k,type(k))
    print(v,type(v))
    print(cfg.items(k))
    print("~~~~~~~~~~~~~~~~~~")
print("-"*30)


for k,v in cfg.items("mysqld"):  #指定section
    print(k,type(k))
    print(v,type(v))
    print("~~~~~~~~~~")

tmp = cfg.get("mysqld","port")
print(tmp, type(tmp))
print(cfg.get("mysqld", "a"))
print(cfg.get("mysqld", "python" , fallback= "linux"))  #按照类型，fallbac：给与缺省值

tmp = cfg.getint("mysqld", "port")
print(type(tmp), tmp)

cfg.add_section("test")
cfg.set("test","test1","1")
cfg.set("test","test2","2")
with open(newfilename,"w+",newline="") as f:
    cfg.write(f)

print(cfg.getint("test" , "test1"))

cfg.remove_option("test", "test1")
# cfg.remove_section("test")
# print("x" in cfg["test2"])
#字典操作
cfg["test3"] = {"c":"1000"}   #没有落地，在内存中修改

print("x" in cfg["test"])
print("c" in cfg["test3"])

# 其他内部方式
print(cfg._dict)  # 返回默认的字典类型，默认使用有序字典

for k, v in cfg._sections.items():
    print(k, v)

for k,v in cfg._sections['mysqld'].items():
    print(k,v)
#重新写入文件
with open(newfilename, 'w') as f:
    cfg.write(f)
```



#### 序列化和反序列化

	要设计一套协议，按照某种规则，把内存中数据保存到文件中，文件是一个字节序列，所以必须把数据转换成<font color=red >字节</font>序列，输出到文件。这就是序列化。反之，从文件的字节序列恢复到内存。就是反序列化。

1. serialization：序列化
   将内存中对象存储下来，变成一个个字节 -->  二进制

2. deseiralization：反序列化
   将文件中的一个个字节恢复成内存中对象   <--二进制

序列化保存到文件就是持久化，可以将数据序列化后持久化，或者网络传输；也可以将文件中或者网络中接收到的字节序列反序列化。

##### pickle库

python中的序列化，反序列化模块。

dumps  对象序列化为bytes对象      dump   对象序列化到文件对象，就是存入文件

loads 从bytes对象反序列化     load  对象反序列化，从文件读取数据

##### 序列化的应用

一般来说，本地序列化的情况，应用较少。大多数场景都应用在网络传输中。 

将数据序列化后通过网络传输到远程节点，远程服务器上的服务将接收到的数据反序列化后，就可以使用了。 

但是，要注意一点，远程接收端，反序列化时必须有对应的数据类型，否则就会报错。尤其是自定义类，必须远程的有一致的定义。

现在，大多数项目，都不是单机的，也不是单服务的。需要通过网络将数据传送到其他节点上去，这就需要大量的序列化、反序列化过程。

但是，问题是，Python程序之间还可以都是用pickle解决序列化、反序列化，如果是跨平台、跨语言、跨协议pickle就不太适合了，就需要公共的协议。例如XML、Json、Protocol Buffer等。

不同的协议，效率不同、学习曲线不同，适用不同场景，要根据不同的情况分析选型。

##### Json

	Json(JavaScript Object Notation，JS对象标记)是一种轻量级的数据交换格式。它基于 ECMAScript (w3c组织制定的JS规范)的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。网址: http://json.org/

Json的数据类型

值：双引号引起来的字符串，数值，true和false，null，对象，数组，这些都是值 

字符串：有正负，有整数，浮点数。

对象：无序的键值对的集合，格式{key:value...}，key必须是一个字符串，需要双引号包围这个字符串，value可以是任意合法的值。

数组：有序的值的集合  格式[val1，，，，valn]

```json
{
  "person": [
    {
      "name": "tom",
      "age": 18
    },
    {
      "name": "jerry",
      "age": 16
    }
  ],
  "total": 2
}
```

Json模块

Python支持少量内建数据类型到Json类型的转换

| Python类型 | Json类型 |
| ---------- | -------- |
| True       | true     |
| False      | false    |
| None       | null     |
| str        | string   |
| int        | integer  |
| float      | float    |
| list       | array    |
| dict       | object   |

常用方法

| Python类型 | Json类型                 |
| ---------- | ------------------------ |
| dumps      | Json编码                 |
| dump       | Json编码并存入文件       |
| loads      | Json解码                 |
| load       | Json解码，从文件读取数据 |

一般Json编码的数据很少落地，数据都是通过网络传输，传输的时候，要考虑压缩它，节省流量。本质来说它就是个文本，就是个字符串。

##### MessagePack

MessagePack是一个基于二进制高效的对象序列化类库，可用于跨语言通信。 

它可以像JSON那样，在许多种语言之间交换结构对象。 

兼容 json和pickle。

MessagePack简单易用，高效压缩，支持语言丰富。 

所以，用它序列化也是一种很好的选择。

安装：$pip install msgpack-python

常用方法：

packb 序列化对象。提供了dumps来兼容pickle和json。 

unpackb 反序列化对象。提供了loads来兼容。

pack 序列化对象保存到文件对象。提供了dump来兼容。

unpack 反序列化对象保存到文件对象。提供了load来兼容。

```python
import pickle
import json
import msgpack

d = {"person":[{"name":"tom","age":18},{"name":"jerry","age":16}],"total":2}
j = json.dumps(d)
print(j, type(j), len(j)) # 请注意引号的变化
print(len(j.replace(' ',''))) # 72 bytes 注意这样替换的压缩是不对的
print("-"*30)
p = pickle.dumps(d)
print(p)
print(len(p))  # 101 bytes
print("-"*30)
m = msgpack.dumps(d)
print(m)
print(len(m)) # 48 bytes
print("-"*30)
u = msgpack.unpackb(m)
print(type(u), u)
u = msgpack.loads(m, encoding='utf-8')
print(type(u), u)
```

```
{"person": [{"name": "tom", "age": 18}, {"name": "jerry", "age": 16}], "total": 2} <class 'str'> 82
72
------------------------------
b'\x80\x03}q\x00(X\x06\x00\x00\x00personq\x01]q\x02(}q\x03(X\x04\x00\x00\x00nameq\x04X\x03\x00\x00\x00tomq\x05X\x03\x00\x00\x00ageq\x06K\x12u}q\x07(h\x04X\x05\x00\x00\x00jerryq\x08h\x06K\x10ueX\x05\x00\x00\x00totalq\tK\x02u.'
101
------------------------------
b'\x82\xa6person\x92\x82\xa4name\xa3tom\xa3age\x12\x82\xa4name\xa5jerry\xa3age\x10\xa5total\x02'
48
------------------------------
<class 'dict'> {b'person': [{b'name': b'tom', b'age': 18}, {b'name': b'jerry', b'age': 16}], b'total': 2}
<class 'dict'> {'person': [{'name': 'tom', 'age': 18}, {'name': 'jerry', 'age': 16}], 'total': 2}
```

