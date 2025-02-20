
---
title: C#语法_委托与事件
slug:  unity04

#description: unity知识点复习

categories:
    - C#
tags : 
    - unity 面试复习
---
### 委托类型：存有方法的引用，派生自system.Dlegate
* 声明：访问修饰符 delegate 返回类型 委托名(参数列表);
* 赋值：委托类型 委托实例名=new 委托类型(方法名);
```c#
        public delegate void num(string s);
        //static num ps1 = new num(Program.output);
        //或者
        static num ps1 =Program.Output;
       

        static void Main(string[] args)
        {
			ps1+=OutputDouble;
            ps1.Invoke("aaa");
            
            Program.ps1 -= output;
            ps1?.Invoke("bbb");
            Console.ReadLine();
        }
        
        static void Output(string s)
        {
            Console.WriteLine(s);
        }
        static void OutputDouble(string s)
        {
            Console.WriteLine(s+s);
        }

```
### 事件
*声明：访问修饰符 event 委托类型 事件名；  
使用事件有三个步骤：定义事件、订阅事件和触发事件。   
* 1.定义事件：  
定义事件需要一个事先定义一个委托类型来指定事件处理方法的签名；  
对于不需要传递额外信息的简单事件，可以直接使用.net提供的EventHandler委托
```c#
using System;

public class Publisher
{
    // 基于预定义的EventHandler定义事件
    public event EventHandler SomethingHappened;

    protected virtual void OnSomethingHappened()
    {
        // 检查是否有订阅者
        SomethingHappened?.Invoke(this, EventArgs.Empty);
    }

    public void DoSomething()
    {
        // 执行一些操作...
        Console.WriteLine("Publisher is doing something...");
        
        // 触发事件
        OnSomethingHappened();
    }
}
```
如果需要传递自定义数据，可以创建继承自EventArgs的新类，并使用泛型版本的EventHandler<TEventArgs>：
```c#
public class CustomEventArgs : EventArgs
{
    public string Message { get; set; }
    public CustomEventArgs(string message)
    {
        Message = message;
    }
}

public class Publisher
{
    // 使用自定义EventArgs定义事件
    public event EventHandler<CustomEventArgs> SomethingHappened;

    protected virtual void OnSomethingHappened(CustomEventArgs e)
    {
        SomethingHappened?.Invoke(this, e);
    }

    public void DoSomething()
    {
        Console.WriteLine("Publisher is doing something...");
        OnSomethingHappened(new CustomEventArgs("A custom message"));
    }
}
```
* 2.订阅事件  
订阅事件就是注册一个当事件发生时应该执行的方法。用+=运算符完成比较方便：
```c#
public class Subscriber
{
    public Subscriber(Publisher publisher)
    {
        // 订阅事件
        publisher.SomethingHappened += HandleSomethingHappened;
    }

    private void HandleSomethingHappened(object sender, EventArgs e)
    {
        if (e is CustomEventArgs customEventArgs)
        {
            Console.WriteLine($"Received message: {customEventArgs.Message}");
        }
        else
        {
            Console.WriteLine("Event received!");
        }
    }
}
```
* 3.触发事件  
事件由发布者触发。例如，在上述例子中，Publisher类中的DoSomething方法会在适当时候调用OnSomethingHappened方法来触发事件：  
```c#
class Program
{
    static void Main(string[] args)
    {
        var publisher = new Publisher();
        var subscriber = new Subscriber(publisher);

        publisher.DoSomething();

        Console.ReadKey();
    }
}
```

### C#的Action
```c#
namespace System
{
    //
    // 摘要:
    //     封装一个方法，该方法不具有参数且不返回值。
    [TypeForwardedFrom("System.Core, Version=3.5.0.0, Culture=Neutral, PublicKeyToken=b77a5c561934e089")]
    public delegate void Action();
}
```
### C#的Action泛型版本
```c#
namespace System
{
    //
    // 摘要:
    //     封装一个方法，该方法只有一个参数并且不返回值。 若要浏览此类型的.NET Framework 源代码，请参阅Reference Source。
    //
    // 参数:
    //   obj:
    //     此委托封装的方法的参数。
    //
    // 类型参数:
    //   T:
    //     此委托封装的方法的参数类型。
    public delegate void Action<in T>(T obj);
}
```
### C#的Func
```c#
namespace System
{
    //这个是fun的十六个重载中的一个参数一个返回值的泛型重载
    // 摘要:
    //     封装一个方法，该方法具有一个参数，且返回由 TResult 参数指定的类型的值。 若要浏览此类型的.NET Framework 源代码，请参阅Reference
    //     Source。
    //
    // 参数:
    //   arg:
    //     此委托封装的方法的参数。
    //
    // 类型参数:
    //   T:
    //     此委托封装的方法的参数类型。
    //
    //   TResult:
    //     此委托封装的方法的返回值类型。
    //
    // 返回结果:
    //     此委托封装的方法的返回值。
    [TypeForwardedFrom("System.Core, Version=3.5.0.0, Culture=Neutral, PublicKeyToken=b77a5c561934e089")]
    public delegate TResult Func<in T, out TResult>(T arg);
}
```
* 这两个都是委托。如果要使用的方法有参数的话，就选择不同的个数的重载，参数可以选择0个到16个，在unity里面只能4个
|Action|Func<>|
|---|---|
|封装的方法，无返回值|封装的方法，有返回值|  

* unity里面的Action和Func<>，参数最多四个
|UnityAction|UnityFunc<>|
|---|---|
|封装的方法，无返回值|封装的方法，有返回值|
* 小拓展：UnityEvent和Event,unity魔改过后的好处是unity将其序列化了，在检视面板可视化编辑  
[unityEvent和C#Event对比](https://blog.dctewi.com/2020/02/unityevent-vs-csharpevent/)  
[UnityAction和C#Action对比](https://gwb.tencent.com/community/detail/125723)
### C#的EventArgs
```c#
namespace System
{
    //
    // 摘要:
    //     表示包含事件数据的类的基类，并提供用于不包含事件数据的事件的值。
    [ComVisible(true)]
    public class EventArgs
    {
        //
        // 摘要:
        //     提供用于与不包含事件数据的事件的值。
        public static readonly EventArgs Empty;

        //
        // 摘要:
        //     初始化 System.EventArgs 类的新实例。
        public EventArgs();
    }
}
```
### C#的EventHandler
```c#
namespace System
{
    //
    // 摘要:
    //     表示将用于处理不具有事件数据的事件的方法。
    //
    // 参数:
    //   sender:
    //     事件源。
    //
    //   e:
    //     不包含事件数据的对象。
    [ComVisible(true)]
    public delegate void EventHandler(object sender, EventArgs e);
}
```