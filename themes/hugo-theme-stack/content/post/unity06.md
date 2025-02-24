
---
title: C#_设计模式——观察模式
slug:  unity06

#description: unity知识点复习

categories:
    - 设计模式
tags : 
    - unity 面试复习
---  
### 设计模式——观察模式
1.定义  
定义对象之间的一种一对多的依赖关系，使得每当一个对象状态发生改变时其相关依赖对象皆得到通知，并被自动更新  
2.示例
在某多人联机对战游戏中，多个玩家可以加入同一战队组成联盟，当战队中的某一成员受到敌人攻击时将给所有其他盟友发送通知，盟友收到通知后将做出响应  
3.类图说明
![观察模式类图](/observer.png)  
* AbsContorlCenter：抽象控制中心，也就是被观察者，包含AbsPlayer的引用。一个遍历player引用进而通知player的方法，还有玩家add,remove进list的方法  
* ContorlCenter:具体控制中心，被观察者的一部分，实现抽象控制中心的方法。  
* AbsPlayer:抽象玩家，也就是观察者，包含AbsContorlCenter的引用。一个被攻击的方法，一个收到盟友被攻击的方法。  
* Player:具体玩家，也是观察者的一部分。包括一个构造函数，new的时候赋予名字。当player被攻击的时候，调用自己的被攻击的方法，从而调用调用控制中心的Notify方法。盟友收到控制中心发来的Notify方法之后，做出反应调用Update方法。  
```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace observer
{
    public abstract class AbsControlCenter
    {
        public string name;
        public List<AbsPlayer> playerList;
        public abstract void Notify();
        public abstract void Add(AbsPlayer player);
        public abstract void Remove(AbsPlayer player);
    }
    public class ControlCenter : AbsControlCenter
    {
        public ControlCenter(string name)
        {
            playerList = new List<AbsPlayer>();
            this.name = name;
        }
        public override void Add(AbsPlayer Player)
        {
            playerList.Add(Player);
        }
        public override void Notify()
        {
            foreach(AbsPlayer ts in playerList)
            {
                ts.Update();
            }
        }

        public override void Remove(AbsPlayer player)
        {
            foreach(AbsPlayer ts in playerList)
            {
                if (ts.Equals(player))
                {
                    playerList.Remove(player);
                    return;
                }
            }
        }
    }
    public abstract class AbsPlayer
    {
        public string name;
        public ControlCenter controlCenter;
        public abstract void Update();
        public abstract void BeAttacked(AbsControlCenter absControlCenter);
    }
    public class Player : AbsPlayer
    {
        public Player(string name)
        {
            this.name = name;
        }

        public override void BeAttacked(AbsControlCenter controlCenter)
        {
            Console.WriteLine(name + "被攻击了");
            controlCenter?.Notify();
        }
        public override void Update()
        {
            Console.WriteLine("我来帮你了："+ name);
        }
         
    }
    class Program
    {
       public static Random id = new Random();
        static void Main(string[] args)
        {
            
            AbsControlCenter absControlCenter = new ControlCenter("虚拟现实技术班级");
            AbsPlayer absPlayer = new Player("小刘");
            AbsPlayer absPlayer1 = new Player("小黄");
            AbsPlayer absPlayer2 = new Player("小李");
            absControlCenter.Add(absPlayer);
            absControlCenter.Add(absPlayer1);
            absControlCenter.Add(absPlayer2);
            absPlayer.BeAttacked(absControlCenter);
            Console.ReadLine();
        }
    }
}
```
输出内容：  
小刘被攻击了  
我来帮你了：小黄  
我来帮你了：小李  
### 用C#的委托和事件来实现观察者模式

