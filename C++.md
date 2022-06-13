# 1. C++类所占内存大小

**空的类也是会占用内存空间的，而且大小是1**，原因是C++要求每个实例在内存中都有独一无二的地址。

类的数据成员，虚函数（指向虚函数表的指针），对齐方式。

虚继承的虚函数表不会继承，普通继承会。

（一）类内部的成员变量：
普通的变量：是要占用内存的，但是要注意内存对齐（这点和struct类型很相似）。
static修饰的静态变量：不占用内存，原因是编译器将其放在全局变量区。
从父类继承的变量：计算进子类中
（二）类内部的成员函数：
非虚函数(构造函数、静态函数、成员函数等)：不占用内存。
虚函数：要占用4个字节(32位的操作系统)，用来指定虚拟函数表的入口地址。跟虚函数的个数没有关系。父类子类共享一个虚函数指针。

# 2.多态

虚函数的使用时在**父类**指针或者引用情况下才会生效。

构造函数和析构函数是无法实现多态性的.

![image-20220111105225835](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\image-20220111105225835.png)

# 3. 四种强制类型转换

## 3.1 const_cast

1、常量指针被转化成非常量的指针，并且仍然指向原来的对象；
2、常量引用被转换成非常量的引用，并且仍然指向原来的对象；
3、const_cast一般用于修改指针。如const char *p形式。

```c++
#include<iostream>
int main() {
    // 原始数组
    int ary[4] = { 1,2,3,4 };

    // 打印数据
    for (int i = 0; i < 4; i++)
        std::cout << ary[i] << "\t";
    std::cout << std::endl;

    // 常量化数组指针
    const int*c_ptr = ary;
    //c_ptr[1] = 233;   //error

    // 通过const_cast<Ty> 去常量
    int *ptr = const_cast<int*>(c_ptr);

    // 修改数据
    for (int i = 0; i < 4; i++)
        ptr[i] += 1;    //pass

    // 打印修改后的数据
    for (int i = 0; i < 4; i++)
        std::cout << ary[i] << "\t";
    std::cout << std::endl;

    return 0;
}

/*  out print
    1   2   3   4
    2   3   4   5
*/
```

## 3.2 static_cast

1.static_cast 作用和C语言风格强制转换的效果基本一样，由于没有运行时类型检查来保证转换的安全性，所以这类型的强制转换和C语言风格的强制转换都有安全隐患。

2.用于类层次结构中基类（父类）和派生类（子类）之间指针或引用的转换。注意：进行上行转换（把派生类的指针或引用转换成基类表示）是安全的；进行下行转换（把基类指针或引用转换成派生类表示）时，由于没有动态类型检查，所以是不安全的。

3.用于基本数据类型之间的转换，如把int转换成char，把int转换成enum。这种转换的安全性需要开发者来维护。

4.static_cast不能转换掉原有类型的const、volatile、或者 __unaligned属性。(前两种可以使用const_cast 来去除)

5.在c++ primer 中说道：c++ 的任何的隐式转换都是使用 static_cast 来实现。

```c++
/* 常规的使用方法 */
float f_pi=3.141592f
int   i_pi=static_cast<int>(f_pi); /// i_pi 的值为 3

/* class 的上下行转换 */
class Base{
    // something
};
class Sub:public Base{
    // something
}

//  上行 Sub -> Base
//编译通过，安全
Sub sub;
Base *base_ptr = static_cast<Base*>(&sub);  

//  下行 Base -> Sub
//编译通过，不安全
Base base;
Sub *sub_ptr = static_cast<Sub*>(&base);    
```

## 3.3 dynamic_cast

*dynamic_cast*强制转换,应该是这四种中最特殊的一个,因为他涉及到面向对象的多态性和程序运行时的状态,也与编译器的属性设置有关.所以不能完全使用C语言的强制转换替代,它也是最常有用的,最不可缺少的一种强制转换.

```c++
#include<iostream>
using namespace std;

class Base {
public:
    Base() {}
    ~Base() {}
    void print() {
        std::cout << "I'm Base" << endl;
    }

    virtual void i_am_virtual_foo() {}

};

class Sub : public Base {
public:
    Sub() {}
    ~Sub() {}
    void print() {
        std::cout << "I'm Sub" << endl;
    }

    virtual void i_am_virtual_foo() {}

};
int main() {
    cout << "Sub->Base" << endl;
    Sub * sub = new Sub();
    sub->print();
    Base* sub2base = dynamic_cast<Base*>(sub);
    if (sub2base != nullptr) {
        sub2base->print();
    }
    cout << "<sub->base> sub2base val is: " << sub2base << endl;
    cout << endl << "Base->Sub" << endl;
    Base *base = new Base();
    base->print();
    Sub  *base2sub = dynamic_cast<Sub*>(base);
    if (base2sub != nullptr) {
        base2sub->print();
    }
    cout << "<base->sub> base2sub val is: " << base2sub << endl;

    delete sub;
    delete base;
    return 0;
}
/* vs2017 输出为下
Sub->Base
I'm Sub
I'm Base
<sub->base> sub2base val is: 00B9E080   // 注:这个地址是系统分配的,每次不一定一样

Base->Sub
I'm Base
<base->sub> base2sub val is: 00000000   // VS2017的C++编译器,对此类错误的转换赋值为nullptr
*/
```

从上边的代码和输出结果可以看出:
对于从子类到基类的指针转换 ,dynamic_cast 成功转换,没有什么运行异常,且达到预期结果
而从基类到子类的转换 , dynamic_cast 在转换时也没有报错,但是输出给 base2sub 是一个 nullptr ,说明dynami_cast 在程序运行时对类型转换对“运行期类型信息”（Runtime type information，RTTI）进行了检查.
这个检查主要来自虚函数(virtual function) 在C++的面对对象思想中，虚函数起到了很关键的作用，当一个类中拥有至少一个虚函数，那么编译器就会构建出一个虚函数表(virtual method table)来指示这些函数的地址，假如继承该类的子类定义并实现了一个同名并具有同样函数签名（function siguature）的方法重写了基类中的方法，那么虚函数表会将该函数指向新的地址。此时多态性就体现出来了：当我们将基类的指针或引用指向子类的对象的时候，调用方法时，就会顺着虚函数表找到对应子类的方法而非基类的方法。因此注意下代码中 Base 和 Sub 都有声明定义的一个虚函数 ” i_am_virtual_foo” ,我这份代码的 Base 和 Sub 使用 dynami_cast 转换时检查的运行期类型信息,可以说就是这个虚函数。

## 3.4 reinterpret_cast

reinterpret_cast是强制类型转换符用来处理无关类型转换的，通常为操作数的位模式提供较低层次的重新解释！但是他仅仅是重新解释了给出的对象的比特模型，并没有进行二进制的转换！

# 4.重载，重写

## 4.1 区别

**函数重载（overload）**
函数重载是指在一个类中声明多个名称相同但参数列表不同的函数，这些的参数可能个数或顺序，类型不同，但是不能靠返回类型来判断。特征是：
（1）相同的范围（在同一个作用域中）；
（2）函数名字相同；
（3）参数不同；
（4）virtual 关键字可有可无（注：函数重载与有无virtual修饰无关）；
（5）返回值可以不同；

**函数重写（也称为覆盖 override）**
函数重写是指子类重新定义基类的**虚函数**。特征是：
（1）不在同一个作用域（分别位于派生类与基类）；
（2）函数名字相同；
（3）参数相同；
（4）基类函数必须有 virtual 关键字，不能有 static 。
（5）返回值**小于等于**父类，否则报错；
（6）重写函数的访问修饰符可以不同；

返回值小于父类的情况：

多态：父类的虚函数返回类型为父类指针或者引用，子类的重写函数为子类的指针或者引用（协变）

**重定义（也称隐藏）**
（1）不在同一个作用域（分别位于派生类与基类） ；
（2）函数名字相同；
（3）返回值可以不同；
（4）参数不同。此时，不论有无 virtual 关键字，基类的函数将被隐藏（注意别与重载以及覆盖混淆）；
（5）参数相同，但是基类函数没有 virtual关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）；

## 4.2 运算符重载

**不能重载的运算符**

- sizeof运算符  

- :: 作用域解析运算符

- ?: 条件运算符

- . 直接成员运算符

- *成员指针运算符

- const_cast

- tpeid

- dynamstic_cast

- reinterpret_cast

- static_cast
  
  **只能通过成员函数进行重载**

- ​    = 赋值运算符

- ​    () 函数调用运算符
* ​    [] 下标

* \* -> 间接成员运算符

# 5.指针

```c++
int *p[4]; //表示指针数组，有四个元素，每个元素都是整型指针。
int *(p[4]); //指针数组
int (*p)[4]; //表示行指针，所指对象一行有四个元素。
int *p(void); //表示函数，此函数无参，返回整型指针。
int(*P)(void) ;//表示函数指针，可以指向无参，且返回值为整型指针的函数。
```

```c++
#include <stdio.h>
int main(){
   int a[5] = {1, 2, 3, 4, 5};
   int *p = (int *)(&a + 1);
   printf("%d", *(p - 1));
}
//答案为5，&a是指向5个int的指针
```

```c
void func(int *p){
    static int num = 4;
    p = &num
    (*p)--;
}
int main(){
    int i = 5;
    int *p = &i;
    func(p);
    printf("%d", *p);
    return 0;
}

//结果为5
```

```c
#include <stdio.h>
void fun(char **p) {
    int i;
    for (i = 0; i < 4; i++)
        printf("%s", p[i]);
}
main() {
    char *s[6] = {"ABCD", "EFGH", "IJKL", "MNOP", "QRST", "UVWX"};    //指针数组
    fun(s);
    printf("\n");
}
//ABCDEFGHIJKLMNOP
```

int *p = 0;

是指针p指向0x00；

***p++相当于 *(p++)**

```c
main(){ 
    char *p = "abcdefgh", *r;
    long *q;
    q = (long*)p;
    q++;
    r = (char*)q;
    printf("%s\n", r);
}
//efgh,long*四个字节，++后变成efgh
```

# 6.结构体

C++中struct与class相似，可以继承，多态，有构造函数。只不过成员默认是public。

结构体类型的定义格式为：

strcut 结构体名 {成员说明列表} ；

结构体变量的定义有3种形式：

1.第一种，定义结构体型的**同时定义结构体变量**，如：strcut 结构体名 {成员说明列表} 变量；

2.第二种，先定义一个结构体类型，**随后****定义结构体变量**，如：strcut 结构体名 {成员说明列表}；结构体名 变量；

3.第三种，定义一个无名称的结构体类型的**同时定义结构体变量**，如：strcut {成员说明列表}变量；

# 7.构造函数与析构函数

派生类实例化时，先调用**基类**的构造函数，然后是派生类的类**成员变量**构造函数（构造的顺序是按照成员变量的定义先后顺序，而不是按照初始化列表的顺序），最后是**派生类**的构造函数。如果基类不是虚析构函数，释放派生类对象时不执行基类的析构函数。

```c++
class Test {
    int a;
public:
    Test() : a(0) {cout << "void";}
    explicit Test(int i) : a(i) {cout << "int";}
    Test(short s) : a(s) {cout << "short";}
    Test &operator=(int n) {a = n; cout << "operator=";}
};

int main() {
    int n;
    Test a = n;
}
//explicit是显示构造，不会转换；只能Test a(n);
//运算符重载不能用于初始化对象
```

在C++中，每个类都有且必须有构造函数。如果用户没有自行编写构造函数，则C++自动提供一个无参数的构造函数，称为默认构造函数。这个默认构造函数不做任何初始化工作。一旦用户编写了构造函数，则这个无参数的默认构造函数就消失了。如果用户还希望能有一个无参数的构造函数，必须自行编写。

# 8. 类的其他问题

```c++
class A;
int main(){
    A a();    //并不是创建了对象，而是声明了一个函数
}
```

```c++
class A{
    public:
    void test(){printf("test A");}
};
int main()
{
    A *pA = NULL;
    pA->test();
    return 0;
}
/*test函数作为非虚函数，在编译时就确定了。即使pA为null，但是已经声明了类型，就知道pA有个test函数，且test函数里没有用到成员变量，单单一个打印语句是可以运行成功的。*/
```

## 8.1 隐式类型转换

```c++
#include <iostream>
using namespace std;
class D{
    int d;
public: 
    D(int x=1):d(x){}
    ~D(){cout<<"D";}}; 
int main(){ 
    D d[]={3,3,3};
    D* p=new D[2];
    delete[]p;
    return 0; 
}
```

## 8.2 静态成员

类的静态成员是所有类的实例共有的，存储在全局（静态）区，只此一份，不管继承、实例化还是拷贝都是一份。

## 8.3 深拷贝和浅拷贝

这是由于编译系统在我们没有自己定义拷贝构造函数时，会在拷贝对象时调用默认拷贝构造函数，进行的是**浅拷贝**！即对**指针**拷贝后会出现两个指针指向同一个内存空间。

 所以，在对**含有指针成员**的对象进行拷贝时，必须要自己定义拷贝构造函数，使拷贝后的对象指针成员有自己的内存空间，即进行**深拷贝**，这样就避免了内存泄漏发生。

## 8.4 纯虚函数和抽象类

含有纯虚函数的类是抽象类。

抽象类不能被实例化。

抽象类的派生类也可以是抽象类。

# 9.内存

static变量位于全局区，不在堆栈

用运算符 sizeof 可以计算出数组的容量（字节数）,char* str = "hello"; sizeof(str)不能计算出内容的容量，只是指针的容量(char *的大小)

```c++
unsigned int a = 0x1234;
unsigned char b = *(unsigned char *)&a; 
//在 32 位大端模式处理器上变量 b 等于（）
/*
unsigned int a= 0x1234的32位完全表示是0x00001234，在大端（低地址存储高位）处理器上的存储方式为：
由低地址到高地址依次为（假设低地址为0x4000）：
0x4000        0x4001     0x4002      0x4003
00               00           12              34
则&a的值为0x4000， char占一个字节，即b最终所取的值为0x4000地址内存储的内容，故为0x00。

若处理器为小端 （低地址存储低位） 模式，则b的值为0x34。
*/
```

## 9.1 BSS 段和 data段

BSS段（bss segment）通常是指用来存放程序中未初始化全局变量的一块内存区域。BSS是英文Block Started by Symbol的简称。BSS段属于静态内存分配。

静态变量和初始化的全局变量在.data段。

# 10.继承

## 10.1 子类型

一种类型当它至少提供了另一种类型的行为,则这种类型是另一种类型的子类型
在公有继承下,派生类是基类的子类型

## 10.2 访问控制

![image-20220117104259020](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\image-20220117104259020.png)

## 10.3 不能被继承的函数

构造函数

静态成员函数

赋值操作函数（赋值运算符重载）

# 11.运算及运算符

整数与小数运算结果是小数。

三目运算符从右到左运算。

短路效应是指，二元逻辑运算符&& ||，左边真值一旦确定，不会执行右边表达式

后置++即使有括号也是后加。

~是取反

逗号表达的求值顺序是从左向右以此计算用逗号分隔的各表达式的值，最后一个表达式的值就是整个逗号表达式的值，所以（++x，y++）的值将是y++

## 11.1 浮点数

合法的浮点数有两种表示形式：

1.十进制小数形式。他有数字和小数点组成，必须有小数点。例如（123.）（123.0）（.123）。

2.指数形式。如123e3。字母e（或E）之前必须有数字，e后面的指数必须为整数。

3.规范化的指数形式里面，小数点前面有且只有一位非零的数字。如1.2345e8

## 11.2 进制

八进制：前面加0    例如：010 = 1 * 8^1 + 0 * 8^0 = 8(十进制)

# 12. STL

![image-20220119114505250](C:\Users\victor\AppData\Roaming\Typora\typora-user-images\image-20220119114505250.png)

## 12.1 vector

### 12.1.1 访问vector中的数据

使用两种方法来访问vector。
1、  vector::at()
2、  vector::operator[]

at总是做边界检查， operator[] 不做边界检查.

### 12.1.2 erase()

vector中erase的作用是删除掉某个位置position或一段区域（begin, end)中的元素，减少其size，返回被删除元素下一个元素的位置。

### 12.1.3 remove()

vector中remove的作用是将范围内为val的值都remove到后面，返回新的end()值（非val部分的end）,但传入的原vector的end并没有发生改变，因此size也就没有变化。

### 12.1.4 其他

std::vector可以调用shrink_to_fit()归还多余的空间

## 12.2 map

map是一类关联式容器。它的特点是增加和删除节点对迭代器的影响很小，除了那个操作节点，对其他的节点都没有什么影响。对于迭代器来说，可以修改实值，而**不能修改key**。

## 12.3 sort()

STL的sort()算法，数据量大时采用Quick Sort（快速排序），分段递归排序，一旦分段后的数据量小于某个门槛，为避免Quick Sort的递归调用带来过大的额外负荷，就改用Insertion Sort（插入排序）。如果递归层次过深，还会改用Heap Sort（堆排序）。

### **size_type**

是由string类类型和vector类类型定义的类型，用于保存任意string对象或vector对象的长度，标准库类型将size_type定义为unsigned类型。

### size_t

是为了方便系统之间的移植而定义的，它是一个无符号整型，在32位系统上定义为：unsigned int；在64位系统上定义为unsigned long。size_t一般用来计数，sizeof操作符的结果类型是size_t，该类型保证能容纳实现所建立的最大对象的字节大小。它的意义大致是“适用于内存中可容纳的数据项目的个数的无符号整数类型”所以，它在数组下标和内存管理函数之类的地方广泛使用。

### **different_type**

一种由vector类型定义的signed整型，用于存储任意两个迭代器间的距离。

### ptrdiff_t

与size_t一样，定义在cstddef头文件中定义的与机器相关的有符号整型，该类型具有足够的大小存储两个指针的差值，这两个指针指向同一个可能的最大数组。

### initializer_list

初始化列表， initializer_list是一种标准库类型，用于表示某种特定类型的值的数组。和vector一样，initializer_list也是一种模板类型，定义initializer_list对象时，必须说明列表中所含元素的类型。和vector不一样的是，initializer_list对象中的元素永远是常量值，我们无法改变initializer_list对象中元素的值。

# 13. malloc, new，free, delete

malloc和new的对象在堆区，效率没有直接在栈区创建高。

栈的空间一般为2M，分配太多一般会崩溃。

## 13.1 delete和delete[]

基本数据类型：两者相同

对象数组：都会释放内存，delete只调用一次析构函数，delete[]调用全部次数。

## 13.2 free

```c++
int *p = (int *)malloc(sizeof(int));
p = NULL;
free(p);
//free 可以释放空指针，但是上述情况原来的内存无法释放，导致内存泄漏
```

# 14. 字符串

## 14.1 substr()和find()

substr(x, y)返回从位置x开始，长度为y的字符串，位置x下标从0开始。
find("ch")返回字符ch在主串中的位置，下标从0开始。

## 14.2 字符数组

```c++
 char str[] = "hello";
 cout << sizeof(str) << endl;
 //结果为6
```

# 15.文件

文件指针指向的是一块内存区域，这块区域存储着打开的文件的相关信息，包括文件读取指针当前位置、文件读取缓冲区大小等信息，并不是指向文件的。 

fread(pt,size,n,fp)指从fp指定的文件中读取长度为size的n个数据项，存入pt所指向的内存区

一个.cpp文件会生成一个.obj文件，.h文件不会生成.obj文件

# 16.数组

## 16.1 二维数组的初始化：

```c
int a[][3]={{1,0,1},{},{1,1}};
```

**列数不能为空**

**行优先存储**

## 16.2 char data[0]内存为0

## 16.3 数组溢出不会报错

```c++
int i, a[5];    //i和a[5]地址相同
int a[5], i;    //i是a[0]的前一个地址
```

# 17. IO

cin遇到空格，TAB，回车会结束。

## 17.1 格式控制符

setw(n)用法： 通俗地讲就是预设宽度
   如 cout<<setw(5)<<255<<endl;
   结果是:
   (空格)(空格)255

setfill(char c) 用法 : 就是在预设宽度中如果已存在没用完的宽度大小，则用设置的字符c填充
  如 cout<<setfill('[@')<<<255<<endl;
  结果是:
  @@255

setbase(int n) : 将数字转换为 n 进制.
  如 cout<<setbase(8)<<setw(5)<<255<<endl;
  cout<<setbase(10)<<setw(5)<<255<<endl;
  cout<<setbase(16)<<255<<endl;
  结果是:
  (空格)（空格)377
  (空格)(空格) 255
  (空格)(空格) ff

使用setprecision(n)可控制输出流显示浮点数的数字个数。C++默认的流输出数值有效位是6。

# 18. 宏和内联函数

## 18.1宏和函数的区别：

1. 宏做的是简单的字符串替换(注意是字符串的替换,不是其他类型参数的替换),而函数的参数的传递,参数是有数据类型的,可以是各种各样的类型.
2. 宏的参数替换是不经计算而直接处理的,而函数调用是将实参的值传递给形参,既然说是值,自然是计算得来的.
3. 宏在编译之前进行,即先用宏体替换宏名,然后再编译的,而函数显然是编译之后,在执行时,才调用的.因此,宏占用的是编译的时间,而函数占用的是执行时的时间.
4. 宏的参数是不占内存空间的,因为只是做字符串的替换,而函数调用时的参数传递则是具体变量之间的信息传递,形参作为函数的局部变量,显然是占用内存的.
5. 函数的调用是需要付出一定的时空开销的,因为系统在调用函数时,要保留现场,然后转入被调用函数去执行,调用完,再返回主调函数,此时再恢复现场,这些操作,显然在宏中是没有的.

## 18.2 内联函数与宏的区别：

1.内联函数在运行时可调试，而宏定义不可以;
2.编译器会对内联函数的参数类型做安全检查或自动类型转换（同普通函数），而宏定义则不会； 
3.内联函数可以访问类的成员变量，宏定义则不能； 
4.**在类中声明同时定义的成员函数，自动转化为内联函数。**

## 18.3 宏定义

\#define       定义一个预处理宏
\#undef       取消宏的定义
\#if          编译预处理中的条件命令，相当于C语法中的if语句
\#ifdef        判断某个宏是否被定义，若已定义，执行随后的语句
\#ifndef       与#ifdef相反，判断某个宏是否未被定义
\#elif         若#if, #ifdef, #ifndef或前面的#elif条件不满足，则执行#elif之后的语句，相当于C语法中的else-if（扩展条件）
\#else        与#if, #ifdef, #ifndef对应, 若这些条件不满足，则执行#else之后的语句，相当于C语法中的else（扩展条件）
\#endif       #if, #ifdef, #ifndef这些条件命令的结束标志.
#defined     　与#if, #elif配合使用，判断某个宏是否被定义

## 18.4 #pragma

#pragma comment。将一个注释记录放置到对象文件或可执行文件中。
#pragma pack。用来改变编译器的字节对齐方式。
#pragma code_seg。它能够设置程序中的函数在obj文件中所在的代码段。如果未指定参数，函数将放置在默认代码段.text中
#pragma once。保证所在文件只会被包含一次，它是基于磁盘文件的，而#ifndef则是基于宏的。

# 19. 其他

## 19.1 volatile

volatile提醒编译器它后面所定义的变量随时都有可能改变，因此编译后的程序每次需要存储或读取这个变量的时候，都会直接从变量地址中读取数据。如果没有volatile关键字，则编译器可能优化读取和存储，可能暂时使用寄存器中的值，如果这个变量由别的程序更新了的话，将出现不一致的现象。可以和const一起用。

## 19.2 回调函数

[菜鸟教程](https://www.runoob.com/w3cnote/c-callback-function.html)

# 20. 模板

声明模板的格式为：template<类型 形参名1，类型 形参名2...>，类型有class和typename

# 21. 英文术语

parallel    并行
