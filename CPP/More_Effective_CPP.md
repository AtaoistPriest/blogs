# Introduce

​	本文为侯捷所作的《More Effective C++ 中文版》的读书笔记，基于 C++98，开卷有益，一起进步。

# Item1 指针与引用

**指针** ：

* **定义**：指针是一个**变量**，其值是另一个变量的**内存地址**。它本身占用存储空间（通常是 4 或 8 字节，取决于系统）
* **语法**：
  * **声明**：`类型* 指针变量名;` (例如：`int* p;`)
  * **取地址**：使用 `&` 操作符获取变量的地址。（例如：`p = &a;`）
  * **解引用**：使用 `*` 操作符访问指针所指向地址的值。（例如：`int b = *p;`）
  * **空指针**：可以将其指向 `nullptr`（C++11 推荐）或 `NULL`，表示不指向任何有效对象。

**引用** ：

*  **定义**：引用是一个变量的**别名**。它为已存在的对象提供了另一个名字。引用本身**不占用额外的存储空间**（在底层实现上通常类似于常量指针，但这是编译器行为，语言层面不讨论其存储）。 
* **语法**：
  * **声明**：`类型& 引用变量名 = 目标变量;` (例如：`int& r = a;`)
  * **关键点**：
    1. **必须在声明时初始化**，且一旦绑定到一个变量，就不能再成为其他变量的引用。
    2. 所有对引用的操作，都等同于对原目标变量的操作。

**指针 vs 引用**：

* 引用必须被初始化，且初始化之后无法改变；而指针可以被重新赋值；

* 引用指向的对象必须不为空！若存在为空的情况，建议使用指针（指针也建议初始化），否则会出现意料之外的现象；

* 若可以保证引用对象不为空，则引用的代码效率比使用指针的要高，其避免了合法性检查；

  ```cpp
  void printDouble(double &num)
  {
      // 无需检查 num，其肯定包含一个 double 类型的值
      std::cout << num << std::endl;
  }
  
  void printDouble(double *num)
  {
      // 检查是否为 NULL
      if ( num )
      {
          std::cout << *num << std::endl;
      }
  }
  ```



# Item2 类型转换

​	关于 CPP 中类型转化的说明还可参考: [强制类型转换](强制类型转换.md) 

​	首先要明确的是 C 语言中的类型转换并不适用与 CPP。

​	CPP 提供了四种新的类型转换操作符克服了 C 风格类型转换的缺点：`static_cast` , `dynamic_cast` , `const_cast` 和`reinterpret_cast` 。

​	对于熟悉 C 风格的程序员可以将 `(type)expression` 替换为 `static_cast<type>(expression)` 。**`static_cast ` 在功能上基本与 C 风格的类型转换一致**，但存在如下不同：

​	（1）不能用 `static_cast ` 如 C 风格的类型转换一样把 `struct`  转换成 `int`  类型或者把 `double`  类型转换成指针类型;

​	（2） `static_cast ` 不能从表达式中去除 `const`  属性，**而`const_cast`  的用途就是转换掉对象的 const 属性**

---

​	 `dynamic_cast` 用于安全地沿着类的继承关系向下进行类型转换，也就是可以把指向基类的指针或引用转换成指向其派生类或其兄弟类的指针或引用。值得一提的是，该操作符可以反馈转换结果，失败的转换将返回空指针（当对指针进行类型转换时）或者抛出异常（当对引用进行类型转换时）。

​	`dynamic_cast` 依赖于**运行时类型信息（RTTI）**，所以只能用于**包含虚函数**的类（多态类型），因为 RTTI 需要虚函数表。

---

​	 `reinterpret_cast ` 转 换 结 果 几 乎 都 是 执 行 期 定 义 （ implementation-defined ）。 因 此 ， 使 用`reinterpret_casts` 的代码很难移植。`reinterpret_casts`  的最普通的用途就是在函数指针类型之间进行转换。

```cpp
typedef void(*FuncPtr)();

FuncPtr funcPtrArray[10];

/*
若需要将 doSomething 存入 funcPtrArray 中，必须进行类型转换，因为 doSomething 函数对于 funcPtrArray 数组来
说有一个错误的类型。在 FuncPtrArray 数组里的函数返回值是 void 类型，而 doSomething 函数返回值是 int 类型。
*/
int doSomething();

funcPtrArray[0] = &doSomething; // 错误！类型不匹配

/*
下面代码可以运行，但转换函数指针的代码是不可移植的（C++不保证所有的函数指针都被用一样的方法表示），在一些情况下这样的转换会产生不正确的结果（参见条款 M31），所以你应该避免转换函数指针类型，除非你处于着背水一战和尖刀架喉的危急时刻。
*/
funcPtrArray[0] = reinterpret_cast<FuncPtr>(&doSomething); 
```

















