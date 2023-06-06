# Learn C++

## C++语法
[std::forward](#std::forward)  
[类型转换](#type_cast)  
###  std::forward
    `std::forward`完美转发，将左值引用转发后为左值引用，右值引用转发后为右值，避免右值被转换为左值的情况。
#### 代码块：
```
#include <iostream>
#include <vector>
#include <string>
class A {
  public:
    A(){}
    A(size_t size): size(size), array((int*) malloc(size)) {
        std::cout 
          << "create Array，memory at: "  
          << array << std::endl;
        
    }
    ~A() {
        free(array);
    }
    A(A &&a) : array(a.array), size(a.size) {
        a.array = nullptr;
        std::cout 
          << "Array moved, memory at: " 
          << array 
          << std::endl;
    }
    A(A &a) : size(a.size) {
        array = (int*) malloc(a.size);
        for(int i = 0;i < a.size;i++)
            array[i] = a.array[i];
        std::cout 
          << "Array copied, memory at: " 
          << array << std::endl;
    }
    size_t size;
    int *array;
};
template<typename T>
void warp(T&& param) {
    if(std::is_rvalue_reference<decltype(param)>::value){
        std::cout<<"param is rvalue reference\n";
    }
    else std::cout<<"param is lvalue reference\n";
    A y = A(param);
    A z = A(std::forward<T>(param));
}
int main(){
    A a = A(100);
    warp(std::move(a));
    return 0;   
}

//----------------output----------------
// create Array，memory at: 0x600002e60000 //main函数中，A a = A(100);调用构造函数
// param is rvalue reference //使用了std::move，根据引用折叠规则，param是一个右值引用
// Array copied, memory at: 0x600002e60070 // A y = A(param); 可以看到调用的是拷贝的构造函数
// Array moved, memory at: 0x600002e60000。// A z = A(std::forward<T>(param)); 调用了移动构造函数

```

### type_cast
>`dynamic_cast`
可向上转换（如基类指针指向派生类），也可向下转换。类型如下：
```
// dynamic_cast
#include <iostream>
#include <exception>
using namespace std;

class Base { virtual void dummy() {} };
class Derived: public Base { int a; };

int main () {
  try {
    Base * pba = new Derived;
    Base * pbb = new Base;
    Derived * pd;

    pd = dynamic_cast<Derived*>(pba);
    if (pd==0) cout << "Null pointer on first type-cast.\n";

    pd = dynamic_cast<Derived*>(pbb);
    if (pd==0) cout << "Null pointer on second type-cast.\n";

  } catch (exception& e) {cout << "Exception: " << e.what();}
  return 0;
}
```    
result
```
Null pointer on second type-cast.
```
### static_cast
staic_cast可向上转换也可向下转换，**无类型安全检查**需要程序员自己保证。（相关联类之间转换）

### reinterpret_cast
可以进行任意指针间的转换 ，还可转换为整数

### const_cast
const_cast 将常量指针转换为非常量，将常量指针作为非常量参数时使用它
```
// const_cast
#include <iostream>
using namespace std;

void print (char * str)
{
  cout << str << '\n';
}

int main () {
  const char * c = "sample text";
  print ( const_cast<char *> (c) );
  return 0;
}
```



