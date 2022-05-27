[TOC]





# 设计原则

## 把会变化的部分取出，"封装"起来

> 如果每次新的需求一来，都会使某方面的代码发生变化，那么你就可以确定，这部分的代码需要抽离出来，和其他稳定的代码有所区别。

## 针对接口/实现编程 是什么意思？

>一个实例，它的行为来自超类的实现，或者用接口并自行实现。这种做法就是依赖于**“实现”**。
>
>一个实例，他的行为来自接口所表达的行为，行为的具体实现会在接口的类上编写好。在运行时才指定具体实现的对象（多态）。——针对接口编程

```ts
abstract class Queen{
    w:Weapon;
    a:Armor;
    constructor(){}
	Fuck(){//假设有这个行为
     	this.w.hit();
        this.a.defence();
    }
    setEquip(newW:Weapon,newA:Armor){//动态更换->策略<-
        this.w = newW;
        this.a = newA;
    }
}
interface Weapon{//WeaponA/B/C实现了它，并完成对应的hit行为
	hit();
}
class WeaponA implements Weapon{
    hit(){con("A hit")} 
}
class WeaponB implements Weapon{
    hit(){con("B hit")} 
}
interface Armor{//ArmorA/B/C实现了它，并完成对应的defence行为
    defence();
}
class ArmorC implements Armor{
    defence() {con("C defence")}            
}
class ArmorD implements Armor{
    defence() {con("D defence")}            
}
class QueenL extends Queen{
    constructor(){
        super();
        this.w = new WeaponA();
        this.a = new ArmorC();
    }
}
let a = new QueenL();
a.Fuck();
let newW = new WeaponB;
let newA = new ArmorD;
a.setEquip(newW,newA);
a.Fuck();
```

而针对实现编程 代码如下。

```ts
class NotGoodQueen implements Weapon,Armor{
    hit() {
        
    }
    defence() {
        
    }
    Fuck(){
        
    }
}
```

## - 策略模式——多用组合，少用继承

> 定义了算法族，分别封装起来，让他们之间可以互相替换，此模式让算法的变化独立于使用算法的客户。

## 策略模式优点

1. 让[角色属性]变得好维护，让[属性]集中在一个类下管理。
2. 计算公式的替换更加方便

- 面对变化时，添加一种新策略即可。甚至能设置多种策略让他人调试。



## -观察者模式——事件系统*

> 定义了对象之间的一对多依赖，这样一来，当一个对象改变状态时，他的所有依赖者都回收到通知并更新。

```ts
class View{
    obj:Objcter
    key:string|number;
    constructor(obj:Objcter,key){
        this.obj = obj;
        this.key = key
        this.obj.AddViewer(this,key)
    }
    remove(){this.obj.DelViewer(this.key)}
    update(str){}
}
class View1 extends View{
    update(str){LOG("1号:"+str)}
}
class View2 extends View{
    update(str){LOG("2号:是"+str+"来了")}
}
class Objcter{    
    viewList:{[key:number]:View} = {};
    AddViewer(er:View,key:string|number){
        this.viewList[key] =er;
    }
    DelViewer(key){
        delete this.viewList[key];
    }
    Notify(str:string){
        for(let i in this.viewList){
            this.viewList[i].update(str);
        }
    }
}
let obj = new Objcter();
let apple = new View1(obj,100);
let bnn = new View2(obj,22);
obj.Notify("年兽");
bnn.remove();
obj.Notify("年兽");
```

进阶版，可以独立通知某个观察者，或者先通过改变的数据通知对应的观察者。

数据来源可以把实例引用传入`SetParam()`方法中，Subject实现类可以保留`中介者/子系统`的引用，从而获得更多数据。

Observer实现类保留`子界面/子系统`目标引用，Update时用Subject获得数据来实现目标更新。



```c#
//游戏事件系统——订阅机制
public abstract class IGameEventSubject{
    private List<IGObserver> m_Obversers = new List<..>();
    private System.Object m_Param = null;
    //Attach(IG duoTai)->Add(duoTai)
    //Detach(IG duoTai)->Remove(duoTai)
    //Notify()->foreach(.Update());
    public virtual void SetParam(System.Object param){//提供方法来设置每个游戏事件所需提供的内容
        m_Param = param;
    }
}

//使用枚举类来管理
pulic enum ENUM_GameEvent{NUll=0,EnemyKilled=1,...}
//管理者
class System{//GameApp
 	private Dictionary<ENUM,IGameEventSubject> m_GameEvents = new..;
    //需要保证主题对象只有一个
    public void RegisterObserver(ENUM,IGObserver){//AddCmd(ENUM,function&caller)
        //ContainsKey->return[ENUM] or new Subject(depend on ENUM),用switch判断,生成不同的Subject
        //Diction add(contanin this [ENUM] now)
    }
    public Notify(ENUM,param){//data
	      //if ContainsKey,Dictionary[ENUM].SetParam(param)
    }
}
public class EnmyKilledSubject:IGS{
    //被观察者们所依赖的对象
    private int m_killCount = 0;
    private IEnemy m_Enemy = null;
    //提供给观察者们“拉”的方法
    public IEnemy GetEnemy(){
        return m_Enemy;        
    }
	public int getCount(){
        return m_killCount;
    }
    public ovrr void SetParam(data){
        base.SetParam(data);
        m_Enemy = data as IEnemy;//c# 引用转换/装箱转换
        m_killCount ++;
        Notify();
    }    
}

```

```ts
class GameApp{    
    uiCmdList:EventDispatcher
    cmdList:EventDispatcher
    public AddCmd(cmd: UICmd | Cmd, cb: Function, caller: any) {
            if (UICmd[cmd]) this.uiCmdList.Add(cmd, cb, caller);
            else this.cmdList.Add(cmd, cb, caller);
        }

        public RemoveCmd(cmd: UICmd | Cmd, cb: Function, caller: any) {
            if (UICmd[cmd]) this.uiCmdList.Remove(cmd, cb, caller);
            else this.cmdList.Remove(cmd, cb, caller);
        }

        public Notice(cmd: UICmd | Cmd, ...data: any[]) {
            if (UICmd[cmd]) {
                this.uiCmdList.Notice(cmd, data);
            } else {
                this.cmdList.Notice(cmd, data);
            }
        }
}
class EventDispatcher {
    private notifiers: Array<Notifier>;

    public constructor() {
        this.notifiers = new Array<Notifier>();
    }
    public Clear() {
        this.notifiers = [];
    }
    public Add(cmd: number, eventCallback: Function, caller: any) {
        for (var i = 0; i < this.notifiers.length; i++) {
            var not = this.notifiers[i];
            if (not.cmd == cmd && not.eventCallback == eventCallback && not.caller == caller) return;
        }
        var notifier = new Notifier(cmd, eventCallback, caller);
        this.notifiers.push(notifier);
    }

    public Remove(cmd: number, eventCallback: Function, caller: any) {
        for (var i = this.notifiers.length - 1; i >= 0; i--) {//倒序珂海星
            var not = this.notifiers[i];
            if (not.cmd == cmd && not.eventCallback == eventCallback && not.caller == caller) {
                this.notifiers.splice(i, 1);
                break;
            }
        }
    }

    public Notice(cmd: number, data: any[]) {
        for (var i = this.notifiers.length - 1; i >= 0; i--) {
            var not = this.notifiers[i];
            if (!not) {
                this.notifiers.splice(i, 1);
                return;
            }
            if (not.cmd == cmd) {
                if (not.eventCallback != null) {
                    not.eventCallback.apply(not.caller, data);//apply
                }
            }
        }
    }
}

class Notifier {
    public cmd: number;
    public eventCallback: Function;
    public caller: any;

    public constructor(cmd: number, eventCallback: Function, caller: any) {
        this.cmd = cmd;
        this.eventCallback = eventCallback;
        this.caller = caller;
    }

}
```

- `GameApp.Ins.Notice(UICmd.x,data);`等同于`m_PBDGame.NotifyGameEvent(emEvent.x,Character)`;，后者在具体的地方使用。传入想传的数据。

- `GameApp.Ins.AddCmd(UICmd.x,function,caller)`相当于`m_PBDGame.RegisterGameEvent(ENUM.x,new xObserverAchievement(this))` 

## 使用观察者模式的优点

> 成就系统以"游戏事件"为基础，记录每个游戏事件发生的次数及时间点，作为成就项目的判断依据。但是当同一个游戏事件被触发后，可能不只是一只有一个曾就系统会被触发，系统中也可能存在其他系统需要使用同一个游戏时间。因此，加入了以观察者模式为基础的游戏事件洗头膏，就可以有效地解除“游戏事件的发生”与有关的“系统功能嗲用”之间的绑定。这样在游戏事件发生时，不必理会后续的处理工作，而是交给游戏实践主体负责调用观察者/订阅者。此外，也能同时调用多个系统同事处理这个事件引发的后续操作。

## 实现观察者模式的注意事项

- 双向与单向通知
- 类过多的问题

在大型项目里可能会产生很多的观察者类，想要减少类的产生，可考虑注册主题时，不使用“类对象“而是使用“回调函数”。之后再将功能相似的回调函数以同一个类管理，减少过多类的问题。

Q&A:想要记录当前连续成功击杀敌人的数量，若有友军阵亡，清零。

> observerxextendsObser{} but it havetowsubject a&b,
>
> how to make:a.notify or b.notify,use a's killedSoilderNOS and b's killEnemyNOS to cult comNOS
>
> then update:the m_ui/midiator to updata(comNOS)

- 和命令模式的**比较**和**整合**

> 命令模式：重点在于管理"命令"，比如新增、删除、记录、排序。整合：让命令模式的执行者可以动态新增、删除。
>
> 观察者：管理“观察者/订阅者”。整合：让观察者模式的每一次发布都可以被管理

# 中介者-Mediator

> GoF:定义一个接口用来封装一群对象的互动行为。中介者通过移除对象之间的引用，来减少它们之间的耦合度，并且能该改变他们之间的互动独立性。

使用中介者模式来集中相关对象之间的复杂的沟通和控制方式。

> 场景：
>
> Bob自动屋，点了一个打盹按钮，闹钟开始计时，通知咖啡壶开始咖啡。即使如此，他的boss还是又不断的要求，周末不和咖啡，洗澡前通电15分钟，丢垃圾的话闹钟要提前。。
>
> 众多的对象之间关系错综复杂。中介者在行动..

- 每个对象都会在自己改变状态时告诉中介者。
- 每个对象都会对中介者所发出的请求做出回应。
- 中介者包含整个系统的控制逻辑。

- 其他应用
  - 网络引擎
  - 数据库引擎

| 同事接口实现类         | 同事接口             | 中介者角色           |
| :--------------------- | -------------------- | -------------------- |
| GamePauseUI界面        | IUserInterface       | Mediator             |
| StageSystem            | IGameSystem          | Mediator             |
| 子系统间用Mediator沟通 | 保留一个Mediator引用 | 集中了大量子系统对象 |

中介者可以搭配其他设计来避免中介接口爆炸的情况。例如游戏事件系统。

中介可以是单例，而不需要在进行一层接口。

---

## 优缺点

1. 优点
   1. 通过将对象彼此解耦，可以增加对象的复用性。
   2. 通过将控制逻辑集中，可以简化系统维护。
   3. 可以让对象之间所传递的信息变得简单而且大幅减少
2. 用途和缺点
   1. 常常用来协调相关的GUI组件
   2. 如果设计不当，中介者对象本身会变得过于复杂。

----

## 面对变化

- 应对变化：当系统/界面需要新增功能时，且该功能需要外界提供信息才能完成时，可以先在中介者类中增加获取信息的方法并完成功能。修改只会在2-3个类中。
- 面对新增：

# 外观——最少知识原则

> 最少知识原则：不要让太多的类耦合在一起，这会让系统变得易碎或复杂。
>
> 外观模式：提供一个统一的接口，用来访问子系统中的一群接口（定义高层接口，让子系统更容易使用）
>
> 想象一个遥控器，一个按钮简化了一系列处理，但我们只知道一个按钮而已。

```java
public float getTemp(){
    return station.getThermometer().getTemperature();
}
```

## 优点

- 易于分工开发
- 增加系统安全性

Q&A

1. 每个系统只能有一个外观吗？

> 可以为一个子系统创建多个外观。

# 外观模式和中介者模式

一个属于结构型。一个属于行为型。

```c#
Battle.Ins.a(){
    PDGame.Ins.Release();//-->PDGame在处理子系统的解除，而Battle只需要知道Release 
}
PDGame{
	private childSystem1;
    private childSystem2;     
    //增加敌人数量
    public void functionC1(){
        c1.Begin();
	}
    //获得某方总血量..
}
childSystem{//实现
	public void Update(){
	    if(_PDGame.xx()){
            Action();
        }
    }

}


```







# 词汇>

OpenID(Facebook,Goole+)、自建账号。。













