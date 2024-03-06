# 第三章 第 7 节 函数

> 原文：[`www.nowcoder.com/tutorial/10031/d4c2644dbe1b434c971444237b39e499`](https://www.nowcoder.com/tutorial/10031/d4c2644dbe1b434c971444237b39e499)

## 2.7、函数

### **2.7.1****定义函数**

C 语言中的函数定义的一般形式如下：

```cpp
return_type function_name( parameter list )
{
   body of the function
}
```

在 C 语言中，函数由一个函数头和一个函数主体组成。下面列出一个函数的所有组成部分：

1、返回类型：一个函数可以返回一个值。return_type 是函数返回的值的数据类型。有些函数执行所需的操作而不返回值，在这种情况下，return_type 是关键字 void。

2、函数名称：这是函数的实际名称。函数名和参数列表一起构成了函数签名。

3、参数：参数就像是占位符。当函数被调用时，您向参数传递一个值，这个值被称为实际参数。参数列表包括函数参数的类型、顺序、数量。参数是可选的，也就是说，函数可能不包含参数。

4、函数主体：函数主体包含一组定义函数执行任务的语句。

### **2.7.2****函数声明**

函数声明会告诉编译器函数名称及如何调用函数。函数的实际主体可以单独定义。

函数声明包括以下几个部分：

```cpp
return_type function_name( parameter list );
```

针对上面定义的函数 max()，以下是函数声明：

 ```cpp
int max(int num1, int num2);
```

在函数声明中，参数的名称并不重要，只有参数的类型是必需的，因此下面也是有效的声明：

```cpp
int max(int, int);
```

当您在一个源文件中定义函数且在另一个文件中调用函数时，函数声明是必需的。在这种情况下，您应该在调用函数的文件顶部声明函数。

### **2****.7.3** **调用函数**

创建 C 函数时，会定义函数做什么，然后通过调用函数来完成已定义的任务。

当程序调用函数时，程序控制权会转移给被调用的函数。被调用的函数执行已定义的任务，当函数的返回语句被执行时，或到达函数的结束括号时，会把程序控制权交还给主程序。

调用函数时，传递所需参数，如果函数返回一个值，则可以存储返回值。例如：

```cpp
#include <stdio.h>

/* 函数声明 */
int max(int num1, int num2);

int main ( )
{
   /* 局部变量定义 */
   int a = 100;
   int b = 200;
   int ret;

   /* 调用函数来获取最大值 */
   ret = max(a, b);

   printf( "Max value is : %d\n", ret );

   return 0;
}

/* 函数返回两个数中较大的那个数 */
int max(int num1, int num2)
{
   /* 局部变量声明 */
   int result;

   if (num1 > num2)
      result = num1;
   else
      result = num2;

   return result;
} 
```

**结果**

Max value is : 200

**查看本章更多知识点**

[`www.nowcoder.com/tutorial/10002/d53a23cbf81d41e59ea888b29d247b07?from=Ccz`](https://www.nowcoder.com/tutorial/10002/d53a23cbf81d41e59ea888b29d247b07?from=Ccz)