[TOC]











# unity项目

## System.IO.File.WriteAllBytes

同时写入同一个文件了，看是否有别的客户端正在打开。



# 前端

## undifined

### TypeError: Cannot read property '10370' of undefined
："不能从undefined读取10370"，所以要处理这个undefined的对象
### 字段名错误会导致undifined，检查大小写

## BaseView错误

![image-20210823222453544](C:\Users\xxx\AppData\Roaming\Typora\typora-user-images\image-20210823222453544.png)

window里的方法调用顺序错误，例如先Close了，但还在调用接口。

## UI错误

![image-20210902210609605](C:\Users\xxx\AppData\Roaming\Typora\typora-user-images\image-20210902210609605.png)

> 可能是新导入的包，没有BindAll();   
>
> 读取组件的路径不正确，生成UI失败，检查OpenWindow类

## 主动调用后端
export let a =["connector","social.chayHandler.getSendlist", ["**fuid**"]]

前面的函数 后面的是参数，必须和后端**同名**，没有则写[]

声明这个方法时：a(**fuid**)  注意**同名**



## 监听服务器

不要忘记在**NetSockets的connector**中加监听的方法,如onChat。

Cmd.ts专门管理服务器中的推，我们做监听就行。要加上自己定义的名字，比如**Msg_onChat**

GameApp.Ins.AddCmd(Cmd.Msg_onChat, this.OnChat,this)任何可被运行的地方

继承了ICmd接口，则直接重写public cmdlist即可UI系统

## 当修改了FGUI项目

如果添加了新包，需要bindAll

<img src="C:\Users\xxx\AppData\Roaming\Typora\typora-user-images\image-20210825185302168.png" alt="image-20210825185302168" style="zoom: 67%;" />





## 读表

TableReader.

GetTableByName(string):ITable——根据名称来选择表

RowByID——返回T，通过匹配sValue的字节得到某一行

```
public RowByID<T>(com: { new(): T }, sValue: any): T | null {
    return this.RowByUnique(com, sValue);
}


```

ITable.RowByUnqyeT(string)——在表中获得“string”区间的字节

> 表的指向
> item.m_nameTitle.color = FairyGUI.Utils.ToolSet.ConvertFromHtmlColor(tbRow._pinjie.color);

![image-20210902114859812](C:\Users\xxx\AppData\Roaming\Typora\typora-user-images\image-20210902114859812.png)

## 代码实例

![image-20210903104238313](C:\Users\xxx\AppData\Roaming\Typora\typora-user-images\image-20210903104238313.png)

3个list一样的item。不一样的item能不能用参数传递？

```
import {
    DevicePerformanceUtil,
    FairyGUI,
    LoadMgr,
    MobileSSPRRendererFeature,
    SoundMgr,
    TimerManager,
    UnityEngine
} from "csharp";
```

```
item.m_nameTitle.color = FairyGUI.Utils.ToolSet.ConvertFromHtmlColor(tbRow.color);
```



```
//shop:IShop
//IShop拥有索引属性，shop[xx]得到一个对象，这个对象的string的key得到IShopItem对象。
declare interface IShop { 
    [shopType: ShopType]: {
        [sid: string]: IShopItem,
    },
    cd: number,     //手动刷新cd结束时间
    num: number,    //手动刷新次数
    t: number,      //每日刷新时间
    v: number,      //商店版本
}
const enum ShopType {
    Daily = 1,
    Gold = 2,
    Box = 3,
    Select = 4,
    Discount = 5,
    Diamond = 6,
    Legend = 8,
}
```





# 资源管理

## 新建 导入导出pairyPackage 发布

> 新建项目之后的文件结构
>
> ![fairy1](C:\Users\xxx\Desktop\biji728\fairy1.png)

> [导入和导出 - 编辑器教程 | FairyGUI](https://fairygui.com/docs/editor/export)

- 导入资源包：fairy package

- 导出资源包：选中多个文件夹，导出

- psd2fgui：安装Node.js的环境，  npm install -g psd2fgui安装nodejs应用

  - ```csharp
    npm install -g psd2fgui
    ```

  - ```csharp
     psd2fgui test.psd 
    ```

  - 生成fairy package

- 包内所有资源都有一个“是否设为导出”的属性，设为导出的资源可以被别的包使用，只有此类组件才能用代码动态生成。设为不导出的资源是不可访问的。

<img src="C:\Users\xxx\Desktop\biji728\fairy2.png" alt="fairy2" style="zoom:75%;" />

- 包的依赖：A包中的某元件使用了B1，则创建A1之前要先载入B包

```csharp
//查询包之间的依赖关系
UIPackage pkg;
var dependencies = pkg.dependencies;
foreach(var kv in dependencies)
{
    Debug.Log(kv["id"]); //依赖包的id
    Debug.Log(kv["name"]); //依赖包的名称
}
```

- 包的发布：

  - 全部发布（发布所有的包）
  - 仅发布定义（不重新生成纹理集，无增删素材时可以用）
  - 发布（发布选中的包）

  <img src="C:\Users\xxx\Desktop\biji728\fairy3.png" alt="fairy3" style="zoom:75%;" />

- 载入包

  ```c#
  //添加包：发布的文件名（在Resource下可以直接用“路径/文件名”）
  //Editor模式下，不论包在哪里，都可以直接用全路径来add
  UIPackage.AddPackage(“file_name”);
  
  //包名：创建对象
  UIPachage.CreateObject(“Package1”,“Component”); 
  ```

<img src="C:\Users\xxx\Desktop\biji728\fairy4.png" alt="fairy4" style="zoom:75%;" />



# FairyGUI编辑器



## 项目、发布设置

> 发布的位置可以直接选



### 项目类型，字体列表(预选)

# 元件

## 元件 基础元件  组合元件

> 在显示列表中，越在下面的元件，反而渲染在越上面。

分好“运动对象”，**创建成component**，然后组合元件，分组（注意选**高级组**）等。

编辑动效结束的时候要**退出**

# 按钮-触发动效

```c#
private GComponent mainUI;
    private GComponent bossCom;
    private GGroup group;
    void Start()
    {
        mainUI = GetComponent<UIPanel>().ui;//获得的是Component Name 的 component
        group = mainUI.GetChild("n2").asGroup;
        bossCom = UIPackage.CreateObject("Package1", "TitleGo").asCom;
        mainUI.GetChild("n0").onClick.Add(() =>
        {
            PlayIt(bossCom);
        });
    }
    private void PlayIt(GComponent targetCom)
    {
        group.visible = false;
        GRoot.inst.AddChild(targetCom);
        Transition t = targetCom.GetTransition("t0");
        //加快动效速度：t.timeScale=2.0f;
        t.Play(() =>
        {
            group.visible = true;
            GRoot.inst.RemoveChild(targetCom);
        });
    }
```

<img src="C:\Users\xxx\Desktop\biji728\fairy5.png" alt="fairy5" style="zoom:75%;" />



## Glist虚拟列表

- 非虚拟列表 ScrollToView(index)

- 虚拟列表

`list.ItemIndexToChildIndex(0):number`该item相对视口0位置的index（显示索引，虽然他可能不在显示区内）,所以可能得到负数

`list.ChildIndexToItemIndex(index):number`用显示索引得到item在数据list的排序index，所以肯定是一个大于0的数。



# UI框架

## 在FairyGUI编辑器中查看结构、导出代码

```typescript
export default class MainShop extends FairyGUI.GComponent{
    //结构中所有的组件
    public m_page: FairyGUI.Controller;//控制器
    public m_towerShop: FairyGUI.GComponent;//类名和成员名的开头(m_)是在全局设置
    public static CreateInstance(): MainShop//组件
    {//这里强转失败？？        
        let ui = <MainShop>(FairyGUI.UIPackage.CreateObject("Main", "MainShop"));
        return ui;
    }
    public init()//成员的赋值
    {
        this.m_page = this.GetController("page");
        this.m_towerShop = <FairyGUI.GComponent>(this.GetChild("towerShop"));
        this.m_normalShop = <FairyGUI.GList>(this.GetChild("normalShop"));
    }
}
```



# 命名约定

## 可关闭、拖拽的窗口子组件命名（例如背包窗口）

| 命名             |          用处          |
| :--------------- | :--------------------: |
| **frame**        |         组件名         |
| >**closeButton** |        关闭按钮        |
| >**dragArea**    |       可拖拽区域       |
| >**contentArea** |        内容区域        |
| **button**       |     按钮控制器名称     |
| **title**        |        文本标题        |
| **icon**         | 图标，通常是一个装载器 |
|                  |                        |

## 会改变的按钮

| 命名   |          元件          |
| ------ | :--------------------: |
| button |      ？？边框？？      |
| title  |        按钮文本        |
| icon   | 改变的图片(装载器名字) |

# TypeScript

如果有ES6引入的新特征，需要使用`tsc --target es6 t.ts`指令编译

- Number包装类型

- String等字符串处理方法

- Array数组方法

- map——类似于无类型限制的字典Dictionary，

  ```typescript
  let map_1=new Map()
  map_1.set("Google",1);
  map_1.set("Baidu",2);
  map_1.set(5,"第五条");
  console.log(map_1.delete("Baidu"));//返回true
  for(let entry of map_1.entries()){//可以循环keys和values
      console.log("map.entries迭代key=>value");
      console.log(entry[0],entry[1])
  }
  for(let[key,value] of map_1){//使用对象解析
      console.log("使用对象解析")
      console.log(key,value)
  }
  map_1.clear()
  ```

  <img src="C:\Users\xxx\Desktop\biji728\typescript2.png" alt="typescript2" style="zoom:80%;" />

- 接口与数组

  ```typescript
  interface namelist{
      [index:number]:string//number改成string，就可以用string做索引值
  }
  var n:namelist=["a","b","c"]
  n[1]="22"//使用索引值访问数组
  ```

- 对象

  ```typescript
  var object1 = {
      key1:"value",//标量
      key2:"value",
      key3:function(){
          //函数
      },
      key4:["a","b"]//集合
  }
  console.log(object1.key1)
  ```

- 类型模板

- 多态-鸭子类型

![typescript](C:\Users\xxx\Desktop\biji728\typescript.png)
