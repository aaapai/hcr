
---
title: C#语法_方法
slug:  unity03

#description: unity知识点复习
categories:
    - C#
tags : 
    - unity 面试复习
---
### 方法
* 结构： 访问修饰符 返回类型 函数名(参数列表){代码块}
```c#
public int GetName(int a){
	a++;
	return a;
}
```  
参数列表用于传输参数，其中包括值参数传递、引用参数传递（ref）、输出型参数传递（out）。默认为值传递，实参和形参不同内存，所以不会影响原始变量。ref参数和out参数，允许方法修改传递给他的参数，并使这些修改反映到调用方法中的原始变量。
|***ref参数***|***out参数***|
|---|---|
|ref倾向于修改已有的值|out主要用于从方法内部输出一个的值|
|以引用传递参数,参数值改变则原始值也改变。使用前必须赋值（形参改了实参也改）|方法体内部必须对out参数赋值，***传递之前不用赋值，赋值了也会被忽略***|  
   
ref参数
```c#
static void ModifyValue(ref int number)
{
    number += 10;
}

// 调用代码
int myNumber = 5;
ModifyValue(ref myNumber);
// 此时myNumber为15
```  
out参数
```c#
static void GetValue(out int number)
{
    number = 20; // 必须赋值
}

// 调用代码
int myNumber;
GetValue(out myNumber);
// 此时myNumber为20
```  
* 递归：  
要素：1.退出条件  2.递归步骤，大化小
```c#
public static int Factorial(int n)
{
    // 退出条件
    if (n == 0)
        return 1;
    else
        // 递归步骤
        return n * Factorial(n - 1);
}
```


