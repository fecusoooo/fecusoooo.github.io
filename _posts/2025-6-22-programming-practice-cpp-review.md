# 类和对象

## 引用

- 引用只能引用变量，不能引用常量和表达式；一定要初始化

- 引用类型作为函数的返回值则可以对返回值赋值

## 常引用

- const T&可以绑定到T或T&或临时对象（而T&无法绑定到const T或const T&,显然，因为T&类型可以用引用修改原变量，而原变量又是const
  的，无法修改，就矛盾了）

```cpp

int a = 5;

const int& cref = a;  // 允许，但无法通过 cref 修改 a

const int& tmp_ref = 42;                // 正确：绑定到字面量

const double& d_ref = 3;               // 正确：绑定到 int→double 转换的临时对象

const std::string& s_ref = "hello";    // 正确：绑定到构造的临时 string

```

- 使用常引用作为函数参数可以1.防止拷贝开销 2.防止修改实参

```cpp

void print(const std::vector<int>& vec) {
    // vec 不能被修改，且避免了拷贝
    for (int num : vec) std::cout << num << " ";

}
```

- 不能返回局部变量的引用

```cpp

// 错误示例：返回局部变量的引用（无论 const 与否）

int& getLocalRef() {
    int x = 10;
    return x;  // 返回后 x 被销毁，引用无效！

}

const int& getLocalConstRef() {
    int y = 20;
    return y;  // 同样错误！

}
```

## 访问权限

1. 在类的成员函数内部, 能够访问：
 当前对象的全部属性, 函数
 同类其它对象的全部属性, 函数

2. 在类的成员函数以外的地方, 只能够访问该类对象的公有成员

## 常量指针和指针常量

1. 常量指针：指向常量的指针

const int* p;

(看作(const int)* p,即指向const int的指针，同理，const int& r也是“常引用”)

2. 指针常量：指针是常量

## 构造函数、复制构造函数与析构函数

- 如果构造函数只有一个参数，也可以写成

```cpp

class Myclass{
    int val;
    public:
        Myclass(int n):val(n){};

};

Myclass a = 3;//

Myclass b(2);

b = 9;//

```

（瓜老师称其为“类型转换构造函数”）如果写成explicit Myclass(int)，那么b = 9和Myclass a = 3;语句报错（禁止隐式转换）

- 复制构造函数的调用时机

1. 用一个对象初始化另一个对象

```cpp

Complex c2(c1);

Complex c2 = c1; //初始化语句

```

2. 非引用类型作为函数参数

3. （不一定）函数返回值为非引用类型

```cpp

// 返回值为值类型

MyClass returnByValue(MyClass obj) {
    return obj; // 会调用复制构造函数

}
// 返回值为引用类型

MyClass& returnByReference(MyClass& obj) {
    return obj; // 不会调用复制构造函数

}
```

对象间用等号赋值不会导致复制构造函数被调用

- 析构函数

- 如果delete一个数组，应写成delete [] p;

- 先构造的后析构

- 析构时机

1. 作用域结束

2. 作为非引用函数参数返回

3. 主函数结束

## 函数重载

- 避免有二义性

## this指针

## 静态成员

### 静态成员变量

- 为所有对象共享，一共一份

- sizeof运算符不会计算其空间

### 静态成员函数

- 不需要具体作用于某个对象

- 访问方式：类名::成员名，也可以类似非静态一样访问，二者无区别

- 静态成员变量需要在类定义外单独声明（可以同时初始化），而静态成员函数不需

- 若静态成员变量时private，则仍需通过接口访问

- 静态成员函数内**不能有非静态成员，不能使用this指针**

## 常量对象与常量方法

- 常量对象只能使用：构造函数、析构函数和常量方法

- 常量方法不能修改非静态成员变量的值（除非加了mutable），只能调用常量方法和静态方法

- 定义和声明时都应带有const关键字

## 函数重载

1. 参数类型

2. 参数数量

3. 有无const

## 封闭类

- 先执行成员对象的构造函数，再执行封闭类的构造函数

- 成员对象构造顺序与在类中的声明顺序一致

## 友元

若A是B的友元类，则A可以访问B的私有成员

## 运算符重载

- +/-可以重载为成员函数/普通函数，后者一般需要可以访问到成员变量,所以一般重载为友元函数

```cpp

class Complex {
    public:
        Complex(double r= 0.0, double i= 0.0):real(r), imaginary(i){}; //constructor
        Complex operator+(int r){// c+5问题
            return Complex(real + r, imaginary);
        }
        friend Complex operator+(int r, const Complex & C);//5+c问题
        //这个函数是Complex类的友元当然要把函数声明写在类的定义里了
    private:
        double real; // real part
        double imaginary; // imaginary part

};

```

- (),[],->只能重载为成员函数

- =只能重载为成员函数,作为赋值运算符/深拷贝运算符

```cpp

String & String::operator = (const char * s){
    delete [] str;
    str = new char[strlen(s)+1];
    strcpy(str, s);
    return *this;

}
```

深拷贝

```cpp

String & operator = (const String & s){
    if( this == &s ) return * this;
    delete [] str;
    str = new char[strlen(s.str)+1];
    strcpy( str, s.str);
    return * this;

}
```

直接对数组拷贝的函数

```cpp

const Array & operator=( const Array & a)

{
    if( ptr == a.ptr ) return * this;
    delete [] ptr;
    ptr = new int[ a.size ];
    memcpy( ptr, a.ptr, sizeof(int) * a.size);//#include<memory>
    // 从a.ptr起sizeof(int) * a.size个字节拷贝到ptr
    size = a.size;
    return * this;

}
```

- 重载类型转换运算符,一般不指定返回类型，形参为空

```cpp

operator int(){
    ///

}
```

- 前置/后置自增/自减运算符

```cpp

CSample& operator++(){
    val++;
    return CSample(val);

}

CSample operator++(int){
    temp = CSample(val);
    val++;
    return temp;

}
```

## 继承与派生

- 派生类无法访问基类的private成员

- protected

```cpp

class Father {
    private: int nPrivate; //私有成员
    public: int nPublic; //公有成员
    protected: int nProtected; // 保护成员

};

class Son : public Father{
    void AccessFather () {
        nPublic = 1; // OK
        nPrivate = 1; // wrong
        nProtected = 1; // OK, 访问从基类继承的protected成员
        Son s;
        s.nProtected = 1; //OK, 访问其它同类对象的基类保护成员
        Father f;
        f.nProtected = 1; //wrong, f不是函数所作用的当前对象
        }

}
```

派生类的成员函数能访问自己/同类的基类的protected对象

- 继承方式

1. 私有继承：基类的public和protected都成为派生类的私有成员

2. 保护继承：基类的public和protected都成为派生类的保护成员

- 构造函数&析构函数

基类先构造后析构

构造顺序：基类、成员对象、派生类

- 派生类对象可以初始化基类/基类指针/基类引用

- 不能通过基类指针访问派生类独有的成员，除非强转

- 由指针/引用类型决定访问的成员函数/成员变量是基类or派生类

# 多态

## 虚函数

- 静态成员函数不能是虚函数

- 如果函数是虚函数，那么由指针指向/所引用对象类型决定调用的是基类还是派生类的虚函数

- 含有纯虚函数的类叫抽象类，不能定义抽象类实例，只能作为指针/引用

- 抽象类的成员函数中可以调用纯虚函数，但是构造函数、析构函数不能调用

- 抽象类的派生类为非抽象类<=>派生类实现了所有纯虚函数

# 输入输出

# 函数模板与类模板

## 函数模板

- 可以在类模板使用多个类型参数避免二义性

- 匹配顺序

1. 参数类型完全匹配的函数

2. 参数完全匹配的模板

3. 没有二义性的前提下，参数经过自动类型转化完全匹配的函数

4. 都找不到，则报错

## 类模板

- 非类型参数，可以提高程序运行效率

- 派生关系

1. 类模板 -> 类模板

2. 普通类 -> 类模板

3. 模板类 -> 类模板

4. 模板类 -> 普通类

# STL

## accumulate函数

```cpp

#include<numeric>

vector<int> v;

accumulate(T1 __first,T1 __last,T2 __init,T3 __binary_op){
    for ( ; __first != __last; ++__first)
        __init = __binary_op(__init, *__first);
    return __init;

}
// 相当于agda中从左向右slash

accumulate(v.begin(),v.end(),0,Sumpowers)

int Sumpowers(int total,int value){
    return total+value*value;

}
```

## strcpy&memcpy

strcpy(dest,src);

memcpy(*dest_p,*src_p,size_t n);

都没有重叠检测
