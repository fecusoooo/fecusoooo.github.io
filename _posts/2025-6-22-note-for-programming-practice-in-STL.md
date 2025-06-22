# 1. 开篇胡扯x

- c++的优势：

1. 面向对象：继承、多态、标准类库

2. 泛型程序设计：模板、标准模板库STL

- STL的基本概念

1. 容器：即类模板

2. 迭代器：可用于依次存取容器中的元素，类似于指针

3. 算法：用于操作容器中元素的函数模板

# 2. 容器

## 2.1. 概述

分为三种：顺序容器、关联容器、容器适配器，前两者又被称为“第一类容器”。

对象被插入容器时，被插入的是对象的复制品

对象所属的类往往需要重载==,<

### 2.1.1. 顺序容器

元素是无序的，插入位置与元素的值无关。

- vector

```cpp

#include<vector>

```

动态数组，存取元素O(n),在尾端增删元素有较佳的性能（因为实际capacity会大于所申请的size）

- deque(double-end queue)

```cpp

#include<deque>

```

双向队列，存取元素O(n),在两端增删元素有较佳的性能

- list

```cpp

#include<list>

```

双向链表，元素在内存不连续存放，在任何位置增删元素都是O(n)，不支持随机存取


### 2.1.2. 关联容器

#### 概览

1. 元素是排序的，插入任何元素都按相应规则插入

2. 查找性能好

3. 以平衡二叉树实现，插入和检索时间都是O(logN)


#### 举例

- map/multimap

pair模板可以用于生成key-value对

```cpp

#include<map>

template<class Key,class T,class Pred=less<Key> >

```

后者允许相同元素,其中元素都是pair<Key,T>类型，可以用make_pair(k,t)其中k::typename Key,t::typename T的方式创建。

Pred缺省状态下利用less<Key>模板函数，其内部实现机制利用<号比较，因此要求Key重载operator<;

手动显式写出belike:multimap<string,int,Cmp>,需要定义Cmp类函数。

```cpp

class Cmp{
    bool operator(const string& a,const string& b) const{
        //!!!注意oj上要求比较函数是const的！
        ...
    }

}
```

- multiset

```cpp

#include<set>

template<class Key,class Pred=less<Key> >

```

1. 映射,key/value，根据key对元素排序，可快速根据key检索元素

后者允许相同key的元素

2. 第一个参数Key：容器中每个元素类型；第二个参数Pred，函数对象，缺省类型是less<Key>，决定“小”的定义。

3. less模板，用<比较，所以要求A重载<

```cpp

template<class T>

struct less : public binary_function<T, T, bool> {

bool operator()(const T& x, const T& y)

{ return x < y ; } const;

}; //less模板是靠<来比较大小的

```

- set

插入set已有元素时插入不成功

#### 成员函数（除了以下提到的以外的）

- find：在容器中查找值为val的元素, 返回其迭代器; 如果找不到, 返回end()

- lower_bound(const T& val):查找一个最大的位置it, 使得[begin(), it) 中所有的元素都比val 小

- upper_bound(const T& val):找一个最小的位置it, 使得[it, end()) 中所有的元素都比val 大

- equal_range:同时求得lower_bound和upper_bound

- count：统计多少个元素值与val相等

- insert

```cpp

iterator insert(const T& val);//插入val并返回迭代器

void insert(const_iterator pos,iterator first,iterator last);//插入区间[first,last)在pos前

```


### 2.1.3. 容器适配器

将不适用的顺序容器(如vector，deque，list)变得适用

只能从末尾或头访问元素

- stack

```cpp

#include<stack>

stack<int> stk; //int型栈, 用deque实现

stack<string, vector<string>> str_stk; //string型栈, 用vector实现

stack<string, vector<string>> str_stk(svec); //string型栈, 用vector

//实现, 并且用向量svec初始化

```

栈，FILO

成员函数：push,pop,top

- queue

```cpp

#include<queue>

```

队列，FIFO

- priority_queue

头文件同上

优先级队列，最高优先级元素总是第一个出列

## 2.2. 成员函数

### 2.2.1. 共有的成员函数

- 比较运算符：字典序比较两个容器

- empty

- max_size

- size

- swap：交换两个容器的内容

### 2.2.2. 只在第一类容器中的成员函数

- begin：返回指向第一个元素的迭代器

- end：返回指向最后一个元素后面位置的迭代器

- rbegin：返回指向最后一个元素的迭代器

- rend：返回指向第一个元素前面的迭代器

- erase：删除一个或几个元素

- clear：删除所有元素

### 2.2.3. 顺序容器常用成员函数

- front 返回容器中第⼀个元素的引⽤

- back 返回容器中最后⼀个元素的引⽤

- push_back 在容器末尾增加新元素

- pop_back 删除容器末尾的元素

- erase 删除迭代器指向的元素(可能会使该迭代器失效),

或删除⼀个区间, 返回被删除元素后⾯的那个元素的迭代器

顺

# 3. 迭代器

指向第一类容器，有const&非const

通过迭代器可以读取指向的元素，非const迭代器还可修改指向的元素

```cpp

容器类名::iterator/const_iterator/reverse_iterator 变量名; //定义一个迭代器

* 迭代器变量名 //访问指向元素

```

- 迭代器上可以执行++操作, 以使其指向容器中的下一个元素

- 如果迭代器到达了容器中的最后一个元素的后面

迭代器变成past‐the‐end 值，此时再使用它，就会出错

类似于使⽤NULL 或未初始化的指针一样

```cpp

vector<int> v;

v.push_back(1); v.push_back(2);

vector<int>::const_iterator i;

for(i = v.begin();i != v.end();i++) cout<<*i;

```

- 不同容器支持的迭代器功能不同

1. 输⼊: 提供对数据的只读访问

1. 输出: 提供对数据的只写访问

2. 正向: 提供读写操作, 并能⼀次⼀个地向前推进迭代器

3. 双向: 提供读写操作, 并能⼀次⼀个地向前和向后移动

4. 随机访问: 提供读写操作, 并能在数据中随机移动

编号大的迭代器拥有编号小的迭代器的所有功能

- 不同迭代器所能进行的操作

1. 所有迭代器: ++p, p++

2. 输⼊迭代器: * p, p = p1, p == p1, p != p1

3. 输出迭代器: * p, p = p1

4. 正向迭代器: 上面全部

5. 双向迭代器: 上面全部, ‐‐p, p‐‐,

6. 随机访问迭代器: 上面全部, 以及:

- 移动i个单元: p += i, p ‐= i, p + i, p – i

- 大于/⼩于⽐较: p < p1, p <= p1, p > p1, p>= p1

- 数组下标p[i]: p 后⾯的第i个元素的引⽤

# 4. 算法

```cpp

#include<algorithm>

#include<numeric>

```

1. find()函数

```cpp

template<class InIt,class T>

InIt find(InIt first,InIt last,const T& val);

```

- 在区间$[first,last)$,查找等于val的元素，用==判断相等

- 返回值为迭代器，如果找到，则指向该元素；否则等于last

2. 比较大小

- 比较大小用“<”进行，与“>”无关

- 相等：1. 有时等价于 x==y 为真 2.有时等价于x < y 与 y < x同时为假

3. esp. list成员函数

- push_front: 在前⾯插⼊

- pop_front: 删除前⾯的元素

- sort: 排序(list不⽀持STL的算法sort)

- remove: 删除和指定值相等的所有元素

- unique: 删除所有和前⼀个元素相同的元素(要做到⽆重复元素, 还需先sort)

- merge: 合并两个链表, 并清空被合并的那个

- reverse: 颠倒链表

- splice: 在指定位置前⾯插⼊另⼀链表中的⼀个或多个元素,

并在另⼀链表中删除被插⼊的元素
