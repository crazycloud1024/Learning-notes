---
title: python练习
date: 2017-08-11 09:00:00
categories: python
tags: python
---
### 打印图形
#### 打印锥形
```python
#给定一个数字和一个符号，打出锥形
import math
l = input().split()
n  = math.floor(math.sqrt((int(l[0])+1)/2))

for i in range(-n,n+1):
    if i < 0 :
        x = -i
        print(" "*(n-x)+l[1]*(2*x-1))
    elif i>1:
        x = i
        print(" "*(n-x)+l[1]*(2*x-1))
    else:
        print(end="")
z = n**2*2-1
print(int(l[0])-z)
```
    31 *
    *******
     *****
      ***
       *
      ***
     *****
    *******
    0

```python
#坐标法打印锥形
n = 7
e = 7//2
for i in range(-e,e+1):
    x = -i if i < 0 else i
    print(" "*(e-x)+ "*" * (2*x+1))
```
    *******
     *****
      ***
       *
      ***
     *****
    *******

#### 打印菱形
```python
#打印菱形
n = 7
e = -(n//2)
for i in range(e,n+e):
    if i<=0:
        print(" "*abs(i)+"*"*(n+2*i))
    else :
        print(" "*i+"*"*(n-2*i))
```

       *
      ***
     *****
    *******
     *****
      ***
       *

#### 打印闪电
```python
#打印闪电
n = 7
e = n//2
for i in range(-e,e+1):
    if i<0:
        print(" "*abs(i)+ "*" * (e+i+1))
    elif i == 0:
        print("*"*n)
    else:
        print(" "*3+"*"*(e-i+1))
```

       *
      **
     ***
    *******
       ***
       **
       *

#### 打印正方形
```python
#打印一个边长为n的正方形
var =  int(input(">>"))
for i in range(var):
    if i ==0 or i == var-1:
        print("*"*var)
    else:
        print("*" + " "*(var-2) + "*")
    
```

    >>4
    ****
    *  *
    *  *
    ****

```python
n = 4
for i  in range(n):
    if i % (n-1)==0:
        print("*"*n)
    else:
        print("*"+" "*(n-2)+"*")
```

    ****
    *  *
    *  *
    ****

```python
#打印一个边长为n的正方形
n =  int(input(">>"))
e = -(n//2)
for i in range(e,n+e):
    if i == e or i == n+e-1:
        print ("*"*n)
    else :
        print("*"+" "*(n-2)+"*")
```

    >>6
    ******
    *    *
    *    *
    *    *
    *    *
    ******

```python
#统计你输入的每个数字有多少个，按照升序输出
l = input().split()
l = int(l[0])
num = list()
num1 = [0]*10
while l>0:
    x = l % 10
    num1[x] += 1
    num.append(x)
    l //=10
for i in range(10):
    if num1[i] !=0:
        print("{}:{}".format(i,num1[i]))
```

    1231013
    0:1
    1:3
    2:1
    3:2



```python
#读数字
zz = input().split()
a = int(zz[0])
l = ['ling','yi','er','san','si','wu','liu','qi','ba','jiu','fu']
i = 0
li =list()
x = a
y = abs(a)
while abs(x)>0:
    x = abs(x)//10
    i += 1
for j in range(i):
    m = 10**(i-1)
    y = y// m
    li.append(y)
    i -= 1
    y = abs(a) % m

length = len(li)
if a>0:
    for i in li:
        if int(i)<length:
            print("{}".format(str(l[i])),end=" ")
        else:
            print("{}".format(str(l[i])),end="")
else:
    print(str(l[10]),end=" ")
    for i in li:
        if int(i)<length:
            print("{}".format(str(l[i])),end=" ")
        else:
            print("{}".format(str(l[i])),end="")
```

    -123
    fu yi er san

### 三个数排序

```python
#只用判断，三个数排序
l = list()
for i in range(3):
    l.append(int(input(">>")))
if l[0] > l[1]:
    if l[0] > l[2]:
        if l[1]>l[2]:
            print(l[0],l[1],l[2])
        else:
            print(l[0],l[2],l[1])
    else:
        print(l[2],l[1],l[0])
else :
    if l[0] > l[2]:
        print(l[1],l[0],l[2])
    else:
        if  l[1]<l[2]:
            print(l[2],l[1],l[0])
        else:
            print(l[1],l[2],l[0])
    
```

    >>1
    >>3
    >>2
    3 2 1


```python
#使用max函数，三个数排序
l = list()
for i in range(3):
    l.append(int(input(">>")))
while l:
    if len(l) == 1:
        print(l[0])
        break
    m = max(l)
    print(m)
    l.remove(max(l))    
```

    >>2
    >>3
    >>1
    3
    2
    1

```python
#使用sort（）函数，三个数排序
l = list()
for i in range(3):
    l.append(int(input(">>")))
l.sort()
l
```
    >>1
    >>4
    >>2
    
    [1, 2, 4]


### 冒泡法排序（重要）

```python
#优化后的冒泡法（重要）
#l=[1,9,8,5,6,7,4,3,2]
l=[1,2,3,4,5,6,7,9,8]
length = len(num)
count = 0
count_swap = 0

for i in range(length):
    flag = False
    for j in range(length-i-1):
        count += 1
        if l[j] > l[j+1]:
            tmp = l[j]
            l[j] = l[j+1]
            l[j+1] = tmp
            flag = True
            count_swap += 1
    if not flag:
        break
print(l,count,count_swap)
            
```

    [1, 2, 3, 4, 5, 6, 7, 8, 9] 15 1

- 优化点：可以设定一个标记判断此轮是否有数据交换发生，如果没有发生交换，可以结束排序，如果发生交换，则进行下一轮
- 最差的排序情况是，初始顺序和目标顺序完全相反，遍历次数1，...,n-1之和n*（n-）/2
- 最好的排序情况是，初始顺序与目标顺序完全相同，遍历次数n-1
- 时间复杂度O（n^2）


#### 二分法查找
```python
#二分法查找
def banery_serch(list,item):
    low = 0
    high = len(list)-1
    while low <= high:
        mid = (low+high)//2
        giss = list[mid]
        if item == mid:
            print(Ture)
        elif item > mid:
            high = mid - 1
        else:
            low = mid +1
my_list = [1,2,3,5,2,6,4,7,9]

```

### 给定一个数，输入为几位数，并依次输出
```python
 #给定一个数，输入为几位数
num = int(input(">>"))
i = 0
flag = False
while num > 0:
    a = num%10
    print("a",a,end=" ")
    num = num//10
    
#给定一个数字判断有几位
num = int(input(">>"))
i = 1
while num >= 10:
    num = num/10
    i += 1
print("i",i)
```

    >>1234
    a 4 a 3 a 2 a 1 


```python
 #给定一个数，并依此输出\n
x = '02300'
x = int(x)
n = 5
w = 10000
flag = False
for i in range(n):
    y = x // w
    if flag or y:
        flag = True    
        print(y,end=" ")
    x = x % w
    w = w //10
```
    2 3 0 0 


```python
c = int('00211')
w = 10000
length = 5
flag = False
while w:
    t = c //w
    if flag:
        print(t,end=" ")
    else:
        if t:
            print(t,end=" ")
            flag =True
        else:
            length -=1
    c %= w
    w //=10
```
    2 1 1 

### 求均值
```python
a = int(input(">>")) 
l = list() 
num = 0 
for i in range(a): 
    x = int(input('>>')) 
    if x == None: 
        break 
    else: 
        l.append(x) 
        num = num+l[i] 
        print(num/len(l))
```

    >>3
    >>1
    1.0
    >>3
    2.0
    >>4
    2.6666666666666665


### 打印乘法表

#### 打印倒乘法表
```python
#打印倒乘法表
for i in range(1,10):
    print("\t"*(i-1),end="")
    for j in range(i,10):
        print ("{}x{}={:<{}}".format(i,j,i*j,2 if j<2 else 3),end=" ")
    print()
```

    1x1=1  1x2=2   1x3=3   1x4=4   1x5=5   1x6=6   1x7=7   1x8=8   1x9=9   
           2x2=4   2x3=6   2x4=8   2x5=10  2x6=12  2x7=14  2x8=16  2x9=18  
                   3x3=9   3x4=12  3x5=15  3x6=18  3x7=21  3x8=24  3x9=27  
                           4x4=16  4x5=20  4x6=24  4x7=28  4x8=32  4x9=36  
                                   5x5=25  5x6=30  5x7=35  5x8=40  5x9=45  
                                           6x6=36  6x7=42  6x8=48  6x9=54  
                                                   7x7=49  7x8=56  7x9=63  
                                                           8x8=64  8x9=72  
                                                                   9x9=81  

#### 打印正乘法表
```python
#打印九九乘法表：
for i in range(1,10):
    for j in range(1,i+1):
        print("{}x{}={}\t".format(j,i,i*j),end=" ")
    print()
```

    1x1=1	 
    1x2=2	 2x2=4	 
    1x3=3	 2x3=6	 3x3=9	 
    1x4=4	 2x4=8	 3x4=12	 4x4=16	 
    1x5=5	 2x5=10	 3x5=15	 4x5=20	 5x5=25	 
    1x6=6	 2x6=12	 3x6=18	 4x6=24	 5x6=30	 6x6=36	 
    1x7=7	 2x7=14	 3x7=21	 4x7=28	 5x7=35	 6x7=42	 7x7=49	 
    1x8=8	 2x8=16	 3x8=24	 4x8=32	 5x8=40	 6x8=48	 7x8=56	 8x8=64	 
    1x9=9	 2x9=18	 3x9=27	 4x9=36	 5x9=45	 6x9=54	 7x9=63	 8x9=72	 9x9=81	 



```python
#打印九九乘法表：
for i in range(1,10):
    for j in range(1,i+1):
        print(j,"x",i,"=",j*i," ",end=" ")
    print()
```

    1 x 1 = 1   
    1 x 2 = 2   2 x 2 = 4   
    1 x 3 = 3   2 x 3 = 6   3 x 3 = 9   
    1 x 4 = 4   2 x 4 = 8   3 x 4 = 12   4 x 4 = 16   
    1 x 5 = 5   2 x 5 = 10   3 x 5 = 15   4 x 5 = 20   5 x 5 = 25   
    1 x 6 = 6   2 x 6 = 12   3 x 6 = 18   4 x 6 = 24   5 x 6 = 30   6 x 6 = 36   
    1 x 7 = 7   2 x 7 = 14   3 x 7 = 21   4 x 7 = 28   5 x 7 = 35   6 x 7 = 42   7 x 7 = 49   
    1 x 8 = 8   2 x 8 = 16   3 x 8 = 24   4 x 8 = 32   5 x 8 = 40   6 x 8 = 48   7 x 8 = 56   8 x 8 = 64   
    1 x 9 = 9   2 x 9 = 18   3 x 9 = 27   4 x 9 = 36   5 x 9 = 45   6 x 9 = 54   7 x 9 = 63   8 x 9 = 72   9 x 9 = 81   


​      
### 一到五阶乘之和
```python
#一到五阶乘之和
a= 1
sum = 0
for i in range(1,6):
    a *= i
    sum += a
    i += 1
print("sum =",sum )
```


```python
#100内的所有奇数之和
a = 0
for i in range (1,100,2):
    a = a+i
print("a=",a)
```

    a= 2500


```python
#100内的所有奇数之和
a = 0
for i in range(1,100,2):
    if not i%2 == 0:
        a += i
print("a=",a)
```

### 斐波那契数列
#### 打印100以内的斐波那契数列
```python
# 打印100以内的斐波那契数列
i = 0
j = 1
while i < 100:
    print(i,end=" ")
    i,j=j,i+j
```

    0 1 1 2 3 5 8 13 21 34 55 89 

```python
# 打印100以内的斐波那契数列
i = 0
j = 1
while i<=100:
    print (i,end=" ")
    k = i + j
    i = j
    j = k
```
    0 1 1 2 3 5 8 13 21 34 55 89 

#### 求斐波那契数列第101项
```python
#求斐波那契数列第101项
i = 0
j = 1
m = 1
while m<=101:
    i, j = j, i + j
    m += 1
    
print(i) 
```
    573147844013817084101


### 求素数

#### 寻常法
```python
#求10万内的所有素数
import math
n = 100000
count = 1
for i in range(3,n,2):
    if i > 10 and i % 5 ==0:
        continue  #所有大于10 的素数中，个位数只有1，3，7，9，所以此处筛出5结尾的
    for j in range(3,int(math.sqrt(i)+1),2):
        if i % j ==0:
            break
    else:
        count += 1
print (count)
```
    9592

#### 利用素数列表，空间换时间
```python
#存储质数
#合数一定可以分解为几个质数的乘积，2是质数
#质数一定不能整除1和本身之内的整数
#使用质数来取模比利用基数取模计算次数更少
import math
n = 100000
count = 1
primenumber = [2]
for i in range(3,n,2):
    flag = False
    edge = math.sqrt(i)
    for j in primenumber:
        if i % j == 0:
            flag = True
            break
        if j > edge:
            break
    if not flag:
        count += 1
        primenumber.append(i)
print(count)
```

#### 利用孪生素数
```python
#大于3 的素数只有6N-1和6N+1两种形式，如果6N-1和6N+1都是素数便叫作孪生素数
import math
n = 100
count = 2
primenumber = [2,3]
step = 2
x = 5
while x < n:
	for i in range(3,int(math.sqrt(x)+1),2):
		if x % i == 0:
			break
	else:
		count += 1
		primenumber.append(x)
	x += step
	step = 4 if step == 2 else 2
print(count)
print(primenumber)
```

#### 埃式筛素数
```python
#埃式筛素数
def eratosthenes(n):
    IsPrime = [True] * (n + 1)
    IsPrime[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if IsPrime[i]:
            for j in range(i * 2, n + 1, i):
                IsPrime[j] = False
    return {x for x in range(2, n + 1) if IsPrime[x]}

if __name__ == "__main__":
    print (eratosthenes(100))
```
    {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97}


### 杨辉三角

#### 计算杨辉三角，普通法
```python
#计算杨辉三角 普通法
triangle = [[1],[1,1]]
for i in range(2,6):
    swap = triangle[i-1]
    cul = [1]
    for j in range(i-1):
        cul.append(swap[j]+swap[j+1])
    cul.append(1)
    triangle.append(cul)
triangle
```

#### 计算杨辉三角 补0法
```python
#计算杨辉三角 补0法
triangle = [[1]]
n = 7
for i in range(1,n):
    swap = triangle[i-1]+[0]
    cul = [1]
    for j in range(len(swap)-1):
        cul.append(swap[j]+swap[j+1])
    triangle.append(cul)
print(triangle)
```

#### 杨辉三角，对称法
```python
#杨辉三角，对称法
n=6
triangle = [[1],[1,1]]
for i in range(2,n):
    tmp = triangle[-1]
    cul = [1] * (i+1)
    for j in range(i//2):
        cul[j+1] = tmp[j]+tmp[j+1]
        if i != 2j:	
            cul[-j-2] = cul[j+1]
    triangle.append(cul)
triangle
```
中点的确定
[1]
[1,1]
[1,2,1]
[1,3,3,1]
[1,4,6,4,1]
[1,5,10,10,5,1]
把整个杨辉三角看成一个左对齐的二维矩阵。
i == 2时，在第3行，中点的列索引j==1
i == 3时，在第4行，无中点
i == 4时，在第5行，中点的列索引j==2
得到以下规律，如果i==2j，则有中点

#### 杨辉三角，单列表方法
```python
#杨辉三角，单列表解决
n = 6
row = [1] * n
for i in range(n):
    z = 1
    offset = n - i
    for j in range(1,i//2+1):
        val = z + row[j]
        z = row[j]
        row[j] = val
        if i != 2*j:
            row[-j - offset] = val
    print(row[:i+1])
```
