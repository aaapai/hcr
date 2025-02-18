
---
title: C#语法_访问修饰符和封装
slug:  unity02

#description: unity知识点复习
categories:
    - C#
tags : 
    - unity 面试复习
---
### 访问修饰符
* public:在所有地方都可以访问，没有访问限制
* private:在类内部可以访问
* protected:在类内部以及派生类访问
* internal:在程序集内部访问，同一个程序时相当于public
* protected internal
### 封装
封装是一种面向对象编程的基本概念，将数据和操作数据的方法绑定在一起，并通过访问修饰符控制对类成员的访问。
例如，将字段声明为private，并提供public方法或属性来间接访问这些字段。  
```c#
public class Person
{
	
    private string name;  
    //法一
    public string Name
    {
        get { return name; }
        set 
        { 
            if (!string.IsNullOrEmpty(value))
                name = value;
            else
                throw new ArgumentException("Name cannot be null or empty");
        }
    }
    //法二
    public string GetName()
   {
            return name;
   }
}
```