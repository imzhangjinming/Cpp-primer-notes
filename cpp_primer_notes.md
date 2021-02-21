# 第七章 类

* 构造和赋值是两个不同的概念

* 构造函数的初始值有时候必不可少，因为有些成员，如**const**、**引用**等，必须进行初始化。

* 成员初始化的顺序是它们在类中出现的顺序

* 如果一个构造函数为**所有**的参数都提供了默认实参，则它实际上也定义了默认构造函数

* exercise 7.36
```C++
MyClass(std::istream& c = std::cin);
```

* 委托构造函数(delegating constructor)

* 隐式的类类型转换只允许进行一步
* 将构造函数声明为`explicit`可以阻止隐式类类型转换，`explicit`只对一个实参的构造函数有效，只能在类内声明构造函数时使用，在类外定义的时候就不需要重复了
* 可以用`static`关键字声明类的静态成员，静态成员由类拥有，而不是为对象所拥有
* 即使一个常量静态数据成员在类内部被初始化了，通常情况下也应该在类的外部定义一下该成员
* 静态成员可以是不完全类型

```C++
class Bar{
public:
	//...
private:
	static Bar mem1;	//正确：静态成员可以是不完全类型
	Bar *mem2;			//正确：指针成员可以是不完全类型
	Bar mem3;			//错误：数据成员必须是完全类型
};
```

* 静态成员可以作为默认实参

# 第八章 IO库  

* IO库类型和头文件   

|头文件 | 类型|  
|:---: | :---: |  
|iostream | istream ostream iostream|  
|fstream | iftream ofstream fstream|  
|sstream | istringstream ostringstream stringstream|  

* IO对象无**拷贝**或**赋值**  
* 由于IO对象无法进行拷贝或赋值，所以不能将形参或返回类型设置为流类型。通常以**引用**方式传递和返回流。读写一个IO对象会改变其状态，因此传递和返回的引用不能是`const`的。  
* 一个流一旦发生错误，其上后续的IO操作也会失败。  
* 每个输出流管理一个缓冲区，缓冲技术主要是为了提升效率，因为单一的写操作可能会很费时，将多个写操作合并成一个会节省很多开销。  
* `std::endl` 可以显式刷新缓冲区  
* `std::endl` `std::flush` `std::ends`都可以显式刷新缓冲区，但是它们的附加符号不同  
* `unitbuf` 和 `nounitbuf`  
* `tie` 可以用来关联流  
* 一个`fstream`对象被销毁时，`close`会被自动调用  
* 保留被`ofstream`打开的文件中已有的数据的唯一方法是显式指定`app`或`in`模式  

# 第九章 顺序容器  
* 顺序容器类型  
	`vector` `deque` `list` `forward_list` `array` `string`  
* `string` 和 `vector` 的元素保存在连续的内存空间中  
* 通常，使用  **`vector`**是最好的选择，除非有很好的理由选择其他容器  
* exercise 9.2  
	`list<deque<int>> L;`  
* `forward_list`迭代器不支持递减运算符  
* 构成迭代器范围的迭代器的限制  
	* `begin` 与 `end` 相等，则范围为空  
	* `begin` 与 `end` 不等，则范围至少包含一个元素，且`begin` 指向该范围中的第一个元素  
	* 可以对`begin` 递增若干次，使得 `begin == end`   
* exercise 9.7  
	`vector<int>::sizetype i;`  
* exercise 9.8  
	`list<string>::const_iterator i;`  
	`list<string>::iterator i;`  
* 当 `auto` 与 `begin` 或 `end` 结合使用时，获得的迭代器类型依赖于容器类型  
* exercise 9.10
	`it1 : iterator`  
	`it2 : const_iterator`  
	`it3 : const_iterator`  
	`it4 : const_iterator`  
* 只有顺序容器的构造函数才接受大小参数，关联容器并不支持  
* 需要交换两个相同类型的容器的内容时，`swap`通常比拷贝快得多  
* 顺序容器（ `array`  除外）定义了一个名为`assign`的成员，允许我们从一个不同但相容的类型赋值，或者从容器的一个子序列赋值。  
* 传递给`assign`的迭代器不能指向调用`assign`的容器（即不能指向它自身？）  
* 除了`string`外，指向容器的迭代器、引用和指针在`swap`操作后不会失效，但是它们指向的元素已经属于不同的容器。  
* 统一使用非成员版本的`swap`是一个好习惯  
* 比较两个容器实际上是进行元素的逐对比较。所以容器的关系运算符使用元素的关系运算符完成比较  
* 向一个`vector`、`string`、`deque`插入元素会使所有指向容器的迭代器、引用和指针都失效  
* `emplace`成员使用传递给它的参数在容器管理的内存中直接构造元素，省去了使用`push`或`insert`时拷贝对象所花费的时间  
* 不能递减`forward_list`迭代器  
* `string` `vector` `deque` `array` 都提供下标运算符  
* `forward_list`部分没有仔细看  
* 使用迭代器添加元素或者删除元素可能会使迭代器失效，因此必须保证每次操作完成后迭代器被**重新定位**  
* `end`返回的迭代器很容易失效，所以最好不要保存`end`返回的迭代器，或者记得实时更新保存`end`返回值的迭代器  
* `vector` `string` `deque` 支持 `shrink_to_fit` 操作，将 `capacity` 减少为与 `size` 相同的大小  
* `string` 的额外操作部分没有细看，需要用到时再回来查吧  
* 容器**适配器**（`adaptor`）
* 每个容器适配器都基于底层容器类型的操作定义了自己的特殊操作，我们**只能**使用适配器操作，不能使用底层容器的操作  
* 
