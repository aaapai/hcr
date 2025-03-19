
---
title: C#_设计模式——简单工厂模式（非二十三种设计模式）
slug:  unity07

#description: unity知识点复习

categories:
    - 设计模式
tags : 
    - unity 面试复习
---  
### 设计模式——简单工厂模式
1.定义  
定义一个工厂类，它可以根据参数的不同返回不同类的实例，被创建的类通常都具有共同的父类。在c#中是用new新建一个类，我在unity里面用Instantiate实例化物体，addcompoent脚本组件代替（最后工厂会返回组件的实例），不然的话不继承monobehaviour是无法作为组件添加到游戏场景中的游戏对象身上的。  
2.示例  
在unity中，通过调整工厂的生产方块和圆球的方法参数，从而实例化方块和圆球，并添加方块和圆球所对应的类   
3.类图说明  
![简单工厂模式类图](/SimpleFactory.png)  
* MonoBehaviour：是unity里面的类，继承于Compoent，也就是组件的一种，从而能够在场景里面通过组合的方式附加到GameObject上面
* Shape:形状接口，针对接口编程，里面可以放一些业务方法，让子类实现。  
* Cube:方块，实现Shape接口。  
* Sphere:圆球，实现Shape接口。  
* Factory:工厂类，用于根据不同的参数（这里是name）实例化Shape预制体，再添加相应的shape脚本，比如Cube预制体就添加Cube脚本，Sphere预制体就添加Sphere脚本       
```c#
public enum Name
{
    Sphere,
    Cube
};
public interface  Shape
{

    public abstract void Log();
}
```
cube脚本
```c#
public class Cube : MonoBehaviour,Shape
{
    public void Log()
    {
        Debug.Log("我是cube");
    }
}

```
sphere脚本
```c#
public class Sphere : MonoBehaviour,Shape
{
    public void Log()
    {
        Debug.Log("我是Sphere");
    }
}
```
Factory脚本，简单工厂模式又叫做静态工厂模式，即工厂方法下面的这个CreateObj方法通常是静态的，然后通过点引用法去调用这个函数，在unity中的这个示例练习，我为了方便，将这里改成非静态的通过Getcommpoent直接获取这个Objfactory的实例进行方法的调用,正式场合个人觉得还是用静态的更简便。
```c#
public class ObjFactory :MonoBehaviour
{
    [SerializeField]
    private GameObject sphere;
    [SerializeField]
    private GameObject cube;
    public  Shape CreateObj(Name name)
    {
        switch (name)
        {
            case Name.Sphere:
                return Instantiate(sphere).AddComponent<Sphere>();
            case Name.Cube:
                return Instantiate(cube).AddComponent<Cube>();
        }
        return null;
    }
}
```
FactoryClient脚本，在这个示例中，要和ObjGactory放在同一个场景中的对象上，因为这样可以获取ObjGactory的实例
```c#
public class FactoryClient : MonoBehaviour
{
    private void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            //ObjFactory.CreateObj(Name.Cube);
            this.gameObject.GetComponent<ObjFactory>().CreateObj(Name.Sphere);
        }
    }
}
```
要点总结：  
* 1、unity中如果只是一些简单的数据类的工厂可以让产品不继承Monobehaviour,如果是产品要附加在场景中的GameObject上面作为组件进行交互的就要继承MonoBehaviour。  

* 2、在工厂类当中，如果是静态的生成产品方法，则只能调用静态的gameobject。那么既然gameobject是静态的，就不可以在检视面板赋值，要读取文件夹，获取预制体，比如这样：sphere = AssetDatabase.LoadAssetAtPath<GameObject>("Assets/Prefabs/sphere.prefab");
  
* 3、为了简化简单工厂模式，可以把产品的抽象父类或者接口和工厂类合并，把静态工厂方法转移到抽象产品那里，直接又是产品又是工厂。在unity中，产品如果要附加MonoBehaviour的话就必须用接口，而不能用抽象父类，所以没有方法体，这种方式无法使用，但是不继承monobehaviour的就可以用。 

* 4、工厂类负责创建的对象比较少不会造成工厂方法中的业务太复杂，客户端只知道传入工厂类的参数对如何创建不关心的时候可以考虑用简单工厂模式。

