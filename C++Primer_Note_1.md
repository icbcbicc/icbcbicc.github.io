## C++ Primer 4th Edition Notes

### chapter 1

- 返回值必须是`int`类型
- 返回值0表示`main`函数成功执行完毕，非0返回值的含义由各个OS自行定义
- 编译
	- Linux: `g++ a.cpp -o a`
	- Windows: `cl -GX a.cpp`
- C++后缀与具体编译器有关：`.cc .ccx .cpp .cp .c`
- 查看main的返回值
	- Linux: `echo $?`
	- Windows: `echo %ERRORLEVEL%`
- iostream
	- `cin`: 标准输入
	- `cout`: 标准输出
	- `cerr, clog`: 标准错误
```c++
#include<iostream>
int main()
{
	std::cout << "Enter 2 nums:" << std::endl;
    int v1,v2;
    std::cin >> v1 >> v2;
    std:cout << v1+v2 << std::endl;
    return 0;
}
```
	- `<<`: 输出操作符，返回左操作数
	- `>>`: 输入操作符，返回左操作数
	- `endl`: 操纵符，可以换行并且flush缓冲区
    - `std::cin >> v1 >> v2;`
    可以转换为
    ```c++
    std::cin >> v1;
    std::cin >> v2;
		```
    - `std:cout << v1+v2 << std::endl;`
    可以转换为

    ```c++
    std::count << v1+v2;
    std::cout << std::endl;
		```

    - 读入未知数目的输入
    ```c++
    int value,sum;
    while(std::cin >> value)
    {
    	sum += value;
    }   
	  ```
	- 输入文件结束符
		- Linux: `ctrl+D`
		- Windows: `ctrl+Z`
- 注释对`/* */`不能嵌套
- 在`for(int count = 0;count<10;count++)`循环中的`count`在循环外不可访问(推荐使用，防止意外访问)

-  use `< >` to include standard lib, use `" "` top include non-standard lib (personal lib)

### chapter 2

- 2 kinds of character
    -  `char`: 8 bits
    - `wchar_t`: 16 bits
- Do not use `char` as a computational type, it may results in some problems
- In many circumstances, `double` runs much more faster than `float`
- Usually, `long double` is too time-consuming and not neccessary
- `short` may leads to some wrap around false
- `int``long``short` represent `signed *`
- `unsigned int` can be simply spelled as `unsigned`
- All characters , including all unprintable characters, can be represented as `\obbb` or `\xddd`. `bbb` are 3 octal numbers and `ddd` are 3 hexadecimal numbers.
-  Every string in C++ will be added with an null character(`\0`) at the end of the string. For example, `"A"` includes an `'A'` and a `\0`, but `'A'` only contains a `'A'`
- wide char: `'L''A'`, wide string: `'L'"A"`
- Concatenate strings(they shoulde be the same kind of string): `"A" "B"`. Join wide string and normal string may causes problpems(Undefinded operation)
- Use `\` to split one line into two, but there is no need doing this if you wanna separate a string into more sub-strings. For example:
``` c++
"asd"
"zxc"
is the same with
"asd \
zxc"
```
All characters(including space or tabs) after the `\` in the folowing line will be treated as part of the string, so the indent after `\` may be incorrect

- 初始化:
	- 复制初始化 `int val = 1024;`
	- 直接初始化 `int val(1024);`
	- 这两种初始化方法差别微妙，但是直接初始化语法更加灵活并且高效
	- 对内置类型来说，两种初始化几乎没有差别

  - 对类型对象来说，有些初始化只能用直接初始化完成。有多个初始化式时不能使用复制初始化（即直接初始化需要多个参数时无法用复制初始化）。
	例如：`std::string a(4,'9');` : `a = “9999”`
	- 在函数体外定义的内置类型会自动初始化为0，在函数内定义的内置函数不初始化
	- 未初始化的变量事实上都有一个值
	- 对于没有提供默认构造函数的类，必须在定义的时候显示地提供初始化式
- 声明与定义
 - 定义：分配空间，指定初始值。一个程序中，一个变量有且只有一个定义（赋值可有多个）
 - 声明：向程序表明变量的类型和名字，变量可声明多次。
 - 定义属于声明，但声明不总是定义
 例如：
 `extern`关键字可用于声明而不定义（也可用于声明并且定义），只有`extern`位于函数外部时，才能含有初始化式（声明并且定义）
 - 在多个文件中使用的变量都需要有与定义分离的声明。一个文件含有定义，使用这个变量的其他文件则包含声明

- `const`限定符：定义常量。因为常量定义后无法修改，因此定义时必须初始化
	- 非`const`变量默认为`extern`，但要使`const`变量能在其他文件中访问，必须显示指定其为`extern const`
- 引用
	- 引用是复合类型，是指用其他类型定义的类型。
	- 不能定义引用类型的引用
	- 引用必须用与该引用 相关联类型的对象（不能是常数） 来初始化（`const`引用除外）
	- 引用只是一个别名，对引用的所有操作都是作用在该引用绑定的对象上
	- `const`引用：可以读取但无法修改，可以绑定其他类型的对象（值可能会发生变化）或者常量
	- 将`const`对象绑定到非`const`引用是非法的


- `typedef`定义类型的同义词
	-例如： `typedef int exam_score`，`exam_score`就可以当做`int`使用

- 枚举(`enumeration`)
	- 关键字: `enum`
	- 例如：`enum modes {input,output,append}`。`modes`式枚举类型名称。默认第一个枚举成员`input`的值为1，后面每个枚举成员的值比前面一个大1。
	- 可以定义枚举成员的初值，例如`enum modes {input=2,output,append}`，其后每个成员依次加1
	- 枚举成员的值可以不唯一。但都是常量，无法修改
	- `enum`定义了一个新的类型，可以定义和初始化其中的枚举成员<br>
	例如： `modes delete = append`<br>
	但只能用枚举成员或者同一枚举类型的其他对象来初始化，不能使用常数<br>
	例如： `modes delete = 3`是错误的

- `class`与`struct`唯一区别在于默认访问级别
	- `class`：每一个成员都默认为`private`
	- `struct`：每一个成员都默认为`public`

- 头文件一般包含
	- 类的定义
	- `extern`变量的声明
	- 函数的声明
	- 由常量初始化的`const`变量
	- 例如：`extern int a = 10;`或者`double b;`都属于定义，不应该出现在头文件中（头文件包含在多个源文件中，直接定义容易产生多重定义错误）
- 编译时将``#include<头文件>`替换为头文件的内容，因此头文件内的变量声明不用`extern`

- 设计头文件时应该保证多次包含同一头文件不会引起多次定义，可以通过使用 头文件保护符 让头文件安全

- 预处理器变量
	- 通常全部大写，在程序中必须唯一
	- 使用类名来命名预处理器变量可以避免预处理器变量重名
	- `#define`用于指定一个名字为预处理器变量
	- `#ifndef`用于检测指定的预处理器变量是否未定义，如果未定义，则处理其后所有指令直到`#endif`
	- `#define`写在`#ifndef`与`#endif`之间即可保证头文件不被多次包含
	-
