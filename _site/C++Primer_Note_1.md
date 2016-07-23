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