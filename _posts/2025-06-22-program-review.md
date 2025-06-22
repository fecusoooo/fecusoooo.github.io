## L6
int,float,complex,str,tuple类型数据本身都不会被修改
比如`a=5 a=a+1`实际上是把6重新绑定到a
整数可以任意长，
十进制,二进制0b,八进制0o，十六进制0x

bool类型
bool -> int
True 1
False 0

int -> bool
0 -> False
其余都是True
空str,tuple,list,dict都是相当于False
非空都相当于True (个人感觉是默认构造函数构造出来的都是False)
```py
if not ():
    print("ok")
if not {}:
    print("ok")
```

判断浮点数相等
```py
import sys
def equal_float(a,b):
    return abs(a-b)<=sys.float_info.epsilon
    # 最小浮点数间隔，32位机为2e-16
```
防止机器误差

高精度浮点数decimal.Decimal
`b = decimal.Decimal("123.456789976542224338384644921")`
`int(x,base) # 将字符串x转换成整数，进制为base`
repr()
返回对象的“官方”字符串表示形式
输出包含转义符号，类型信息等，便于理解对象内部状态
/ 除法结果一定是小数（即使整除）
// 结果为整数（欧几里得除法）

字符串用单引号、双引号、三引号括起来

字符串的切片`str[x:y]`左闭右开
字符串不可修改，
```py
a = "1234"
a[2] = "2"
```
用 + 连接字符串 `a+b`
用 * 构成重复字符 `b = a*4`
用 `in/not in` 判断子串
`y = int(x)`
`y = float(x)`
`y = str(x)`
`y = eval(x)`
不会改变x的值
count求子串出现的次数: `s.count("AA")`
`len(s)`
`upper`转大写，`lower`转小写
`find/rfind` 查找子串，返回下标，找不到则返回“-1”
`index/rindex` 查找子串，返回下标，找不到则抛出异常
```py
try:
    s.index("afb")
except Exception as e:
    print(e)
```
`replace(x,y)`把所有x替换为y
`isdigit()`,`islower()`,`isupper()`判断字符串是否全是数/小写/大写
`s.startwith(x)`,`s.endwith(y)`判断字符串是否以x开头，y结尾
`s.strip()`返回除去头尾空白字符的字符串
`s.lstrip()`返回去除左部空白字符的字符串
`s.rstrip()`返回去除右部空白字符的字符串

或者带参数版本`s.strip(x)`去除头尾x
`s.split(x)`
利用正则表达式分割，分割串用|隔开
```py
import re
a = "Beautiful is; a, good|quality*too"
print(re.split(';| |,|\||\*',a))
```
相邻分割串会多出一个空串

python缺省认为源代码是utf-8编码的

## L7
- 元组
元组元素不能被修改，但是元素内容可以被修改
`v = ("hello",[1,2,3],[3,2,1])`
`v[1][0] = 'world'`
输出`('hello',['world',2,3],[3,2,1])`

元组的元素都是指针，元组元素不可修改指的是不可改变元组元素的指向，但是元组元素指向的内容可修改

可以用下标访问元组，也可以类似字符串的切片
可以对元组用+连接组合
可以用*进行重复连接构造
- 列表
列表元素可以增删、修改
```py
lst = [1,2,3,4]
del lst[1] <=> lst.pop(1)
lst += [4,5,6]
```
lst 元素也可以是变量类型，比如`lst = [a,n]`
`a = ["hello","world"]`，a如果改变则lst也会改变
元组生成式 `print(tuple(x * x for x in range(1,11)))`

`map(func,seq)` 返回一个延时求值对象
`filter(func,seq)` 按照定义过滤列表/元组一些元素,返回延时求值对象
```py
def f2(x):
    return x%2==0
ls = [1,2,3]
lst = list(filter(f2,ls))
```
`map(func,iterables)`,可以同时去除iterable1,iterble2,,,iterable_n的同一个位置元素组成参数传给func（因而func需要有n个参数）
`reduce(function,sequence,startValue)`将列表以func方式累积起来
```py
def myReduce(function,sequence,startValue):
    for x in sequence:
        startValue = function(startValue,x)
    return startValue
```
可以用`tuple()`,`list()`强制转换列表/元组
切片方式不能进行深拷贝，需要
```py
import copy
b = copy.deepcopy(a)
```
函数可以返回多值（本质上是返回一个元组）
函数参数是形参（按值传递）
参数个数不定的函数
`def func(a,*b)` b是一个元组
`func(*[1,2,3])` `func(*(1,2,3))`
`def func(**b)` b是一个字典
在函数中使用全局变量需要声明`global x,y`

## L8
- 字典
`d.items()`
`d.values()`
`d.keys()`
字典的键不能重复，指的是字典的键的内容不能一样
键必须是不可变的，比如字符串、整数、浮点数、元组

- 集合
集合可修改，没有重复元素，元素无序
整数、小数、复数、字符串、元组都可以作为元组元素
列表、字典、集合等可变数据类型不能作为集合元素
元素必须可哈希，可以用于快速查找，相当于c++哈希表
```py
s.add(x) # 将x加入s，如果重复则不进行任何操作
s.discard(x) # 将x删除，如果不存在，不会引发异常
s.remove(x) # 将x删除，如果不存在，会引发异常
```
```txt
a,b 是集合
x in a
a | b a,b的并
a & b a,b的交

<,>,<=,>=判断包含关系
```

`a = {1,2,2,"ok"} # 集合会自动去重`

- 面向对象
###
```py
class Myclass:
    def __init__(self,*paras)
    def __del__(self)
    # 成员变量可以随时添加
    def __eq__(self)
    def __str__(self):
        return "({0.x} {0.y})".format(self)

    __p = 20 # 私有属性以__开头
```
不可变的内置对象是可哈希的，如元组、字符串
而字典、列表、集合（set）是不可哈希的

### 序列索引重载：
```py
    def __getitem__(self,index): # 右值
    def __setitem__(self,index,val): # 左值
    def __lt__(self,other): # 用于排序
    def __str__(self): # 用于输出
```
`class 派生类名(基类名)`
`isinstance(val,type)`

函数式程序设计
- 函数可以给变量赋值（本身作为变量）
- 函数可以作为函数的参数
- 闭包（带自由变量的函数）
```py
def func(x):
    def g(y):
        nonlocal x #有了此行, 才能在g中对x赋值
        x += 1
        return x + y
    return g
```
nonlocal用于表示这个变量不是局部变量，是外层（非全局）作用域的变量
- 偏应用函数：部分赋值

### 迭代器
- 迭代器必须实现`__iter__()`和`__next__()`方法
```py
x = [1,2,3]
it = iter(x) #获取x上的迭代器
print(next(it)) # 1
print(next(it)) # 2
print(next(it)) # 3
print(next(it)) # 异常
```
可以用try-except语句捕获异常
在for循环中，本质是通过迭代器,next函数，检查异常实现的

```py
class MyRange:
    def __init__(self,n):
        self.n = n
    def __iter__(self):
        return MyRangeIterator(self.n)

class MyRangeIterator:
    def __init__(self,n):
        self.i = 0
        self.n = n
    def __iter__(self):
        return self
    def __next__(self):
        if self.i<self.n:
            i = self.i
            self.i += 1
            return i
        else:
            raise StopIteration()
for i in MyRange(5):
    print(i)
```

### 生成器
调用for循环时才完成计算，比如
`a = (i*i for i in range(5))`
含有yield的函数是一个生成器，next会顺序执行函数的语句直到碰到yield为止


eval 和 exec 可以指定作用域
```py
scope = {}
scope['a'] = 3
scope['b'] = 4
print(eval("a+b+c",scope))
```

1. 方式一
`file = open("C:\\Desktop\\t.txt","w")`
`f.write("xxx")`
2. 方式二
输入输出重定向（略）
