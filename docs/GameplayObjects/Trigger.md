::: info
**阅读本文预计 15 分钟**
:::

# 触发器

> 【本地资源库】中的【游戏功能对象】栏

【触发器】对象在项目中用来检测与其他对象产生的交互行为，在编辑环境中可见，在运行环境中不可见

![](https://cdn.233xyx.com/online/IfQ7aibNW5ZH1694487468870.PNG)

在【触发器】检测到交互行为后，触发器会触发对应交互行为的事件，通过该事件可以将对象与触发器的交互行为与游戏逻辑关联起来。此外你还可以控制触发器的开关来决定触发器是否进行检测，或者查找某个对象是否处于触发器范围内。

::: tip
【触发器】在游戏中实际上是不可见的。编辑状态下【触发器】会有辅助线和颜色填充框进行标识，我们可以动态看到【触发器】形状大小位置旋转发生的变化。运行状态下，可以按下键盘 "~" 键输入 "show Collision"命令来查看带有碰撞盒的对象，包括【触发器】。
:::

# 触发器属性

## 基本属性

【触发器】的**位置**表示 **检测区域的位置** ，**缩放尺寸**表示 **检测区域的大小与形状** ，可通过【属性面板】中的【变换】进行调节。

## 触发器开关

【触发器开关】指的是触发器是否启用，可通过api读取或设置。

获取【触发器】对象后，通过修改其**enabled**属性控制它的开关状态【触发器】。

当【触发器】打开时，它会持续检测区域范围内其他（带碰撞）对象与它的交互行为。当【触发器】关闭时，触发器停止检测。在切换【触发器】开关状态时，触发器会主动检测一次交互行为：假设触发器中存在某一个对象，关闭触发器会触发一次该对象的离开事件，再次打开触发器又会触发一次该对象的进入事件。

```TypeScript
// 获取脚本挂载的对象并以【触发器】对象进行接收
let trigger = this.gameObject as Trigger;
// 输出触发器开关状态
console.log(trigger.enabled);
//关闭触发器
trigger.enabled=false;
```

## 触发器形状

> 【触发器】的【碰撞类型】栏

【触发器形状】共有两种类型：盒体触发器和球体触发器。

该属性可读可写，可以通过属性面板调节以及调用api读取或设置。

1. 属性面板调节

通过下拉菜单进行选择

![](https://cdn.233xyx.com/online/CUeqeuqHU8pZ1694487468870.PNG)
![](https://cdn.233xyx.com/online/fZ2Gv7Sl3k7C1694487468870.PNG)

2. 调用api读取设置

获取【触发器】对象后，通过修改其**shape**属性设置【触发器形状】

| 属性        | 说明         | 取值范围              |
| ------------- | -------------- | ----------------------- |
| `shape` | 触发器的形状 | Box：盒体Sphere：球体 |

```TypeScript
// 获取脚本挂载的对象并以【触发器】对象进行接收
let trigger = this.gameObject as Trigger;
// 输出触发器形状
console.log(trigger.shape);
//将触发器形状设置为球体触发器
trigger.shape=Sphere;
```

## 触发器范围

【触发器范围】指的是触发器所覆盖的范围大小，即是触发器可触发范围，可以由继承自父类`GameObject`的`worldTransform.scale`世界缩放属性和`localTransform.scale`相对缩放属性控制。

当【触发器】形状为盒体时，XYZ值分别表示盒体的长宽高，默认值为（50，50，50）。当【触发器】形状为球体时，XY值无效，Z值表示球体半径，默认值为50。

该属性可读可写，可以通过调节触发器缩放尺寸调节以及调用api读取或设置。

| 属性              | 说明       |
| ------------------- | ------------ |
| `shapeExtent` | 触发器大小 |

| 方法                  | 说明                 |
| ----------------------- | ---------------------- |
| `setBoxExtent`    | 设置盒体触发器长宽高 |
| `setSphereRadius` | 设置球体触发器半径   |

```TypeScript
// 通过接口设置触发器大小
if(SystemUtil.isServer()) {
  // 获取脚本挂载的对象并以【触发器】对象进行接收
  let trigger = this.gameObject as Trigger;
  // 输出触发器范围
  console.log(trigger .shapeExtent);
  //识别触发器类型，若是盒体触发器，将其尺寸调为（100,100,100）
  if(trigger.shape==TriggerShapeType.Box){
    trigger.setBoxExtent(new Vector(100,100,100),true);
    console.log(trigger .shapeExtent);
  }
  //若是球体触发器，将其半径调为50
  else{
    trigger.setSphereRadius(50,true);
    console.log(trigger .shapeExtent);
  }
}

// 通过scale属性设置触发器大小
if(SystemUtil.isServer()) {
     //异步生成【触发器】对象并初始化信息
     let trigger1 = await GameObject.asyncSpawn("Trigger",{replicates: true, transform: new Transform(new Vector(300, 0, 50), Rotation.zero, Vector.one)}) as Trigger;
     //调节本地缩放
     trigger1.localTransform.scale = new Vector(2, 2, 2);
     //调节世界缩放
     trigger1.worldTransform.scale = new Vector(300, 50, 50);
     //打印世界缩放数值
     console.log("trigger worldScale: " + trigger1.worldTransform.scale);
}
```

# 触发器使用

## 触发器工作流程

![](https://cdn.233xyx.com/online/ZQSHobQBG7rQ1694487468870.png)

## 触发器创建方式

资源拖拽创建：从【本地资源库】中的【游戏功能对象】栏将【触发器】拖入【场景】或者【对象管理器】来创建对象。

通过脚本创建：具体方式见 [游戏对象的创建与获取](https://meta.feishu.cn/docx/LnC0dsCrkoXTPZxEoywcvUf7nBg)

::: tip
【触发器】通常作为一个双端同步对象使用，即同一个触发器在所有端都存在。但是触发器检测交互行为并触发事件却是依照各自端的情况进行。所以使用【触发器】时需要注意避免事件中的委托函数重复执行。
:::

## 触发器检验指定对象

我们可以使用checkInArea方法来检验指定对象是否在触发器区域内，并根据检验结果进行后续操作。

返回结果true代表目标对象在触发器区域内，返回结果false代表目标对象没在触发器区域内。

*示例代码*

```TypeScript
// 获取脚本挂载的对象并以【触发器】对象进行接收
let trigger = this.gameObject as Trigger;
//异步生成资源id为"197386"的游戏对象
let object=await GameObject.asyncSpawn("197386") as GameObject
//打印对象是否在触发器区域内检验结果
console.log(trigger.checkInArea(object))
//若对象在触发器区域内，销毁对象
if(trigger.checkInArea(object)){
   object.destroy;
}
```

## 触发器事件

【触发器】有两个事件：进入触发器事件和退出触发器事件。

获取到【触发器】对象后，我们可以通过这两个事件添加委托执行的逻辑。每当一个（带碰撞）对象与触发器发生交互行为时（开始重叠和结束重叠），就会触发对应的事件并执行委托的逻辑。事件中会传出产生交互行为的对象供用户在执行的逻辑中使用。

将下列示例代码替换脚本中的`onStart`方法：示例代码在服务端往`asyncFindgGameObjectById`接口（中传入【触发器】对象的id异步获取了一个对应的【触发器】对象。在该【触发器】对象的进入事件中添加一个函数：如果进入的对象是角色，那么打印一行信息并将角色切换为飞行状态。在该【触发器】对象的离开事件中添加一个函数：如果离开的对象是角色，那么打印一行信息并将角色切换为行走状态。

*示例代码*

```TypeScript
protected async onStart(): Promise<void> {
        if(SystemUtil.isServer()) {
            let trigger = await GameObject.asyncFindGameObjectById("11E8575A") as Trigger;
            console.log("trigger gameObjectId: " + trigger.gameObjectId);
            //进入触发器事件
            trigger.onEnter.add((gameObject: GameObject) => {
                if(gameObject instanceof Character) {
                    let chara = gameObject as Character;
                    console.log("enter chara name " + chara.displayName);
                    chara.switchToFlying();
                }
            });
            //离开触发器事件
            trigger.onLeave.add((gameObject: GameObject) => {
                if(gameObject instanceof Character) {
                    let chara = gameObject as Character;
                    console.log("leave chara name " + chara.name);
                    chara.switchToWalking();
                }
            });
}
```

# 案例：制作一个简单的关卡

1. 打开编辑器，创建一个新的空项目，打开项目
2. 在【资源库】中，选取一些资源拖入场景中，包括一个机关、一个障碍、一个楼梯以及其他场景内物体

![](https://cdn.233xyx.com/online/AauHdLCV6ZFO1694487468869.png)

3. 在【资源库】-【游戏功能对象】中找到【触发器】，拖取两个进入场景中，并分别调整至合适尺寸，放置于机关与楼梯之上

![](https://cdn.233xyx.com/online/uFFlqsw17s491694487468870.png)

4. 在【对象管理器】中将相对应的【触发器】拖拽至对应物体下（将触发器设置为对应物体子对象）

![](https://cdn.233xyx.com/online/IgDxd2qdgjY31694487468870.png)

5. 在【工程内容】-【脚本】中新建脚本，取名为trigger，并打开进行编辑保存

![](https://cdn.233xyx.com/online/4p8Pv0lfemod1694487468870.png)

*脚本示例   ps：对象id需要替换为自己游戏中对应id*

```JavaScript
@Component
export default class trigger extends Script {

     ii:GameObject
    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise<void> {  
            //获取触发器对象
            let trigger = this.gameObject as Trigger;
            //获取台阶上的触发器
            let trigger2 = await GameObject.asyncFindGameObjectById("2889A1E0") as Trigger;
            //获取障碍物
            let door = await GameObject.asyncFindGameObjectById("070718A3") as GameObject;
            //初始化台阶上触发器开关状态：关
            trigger2.enabled=false;
            console.log("trigger gameObjectId: " + trigger.gameObjectId);
            //进入触发器事件
            trigger.onEnter.add((gameObject: GameObject) => {
                if(gameObject instanceof Character) {
                    let chara = gameObject as Character;
                    console.log("enter chara name " + chara.displayName);
                    door.destroy();
                    trigger2.enabled=true;
                }
            });
    }

    /**
     * 周期函数 每帧执行
     * 此函数执行需要将this.useUpdate赋值为true
     * @param dt 当前帧与上一帧的延迟 / 秒
     */
    protected onUpdate(dt: number): void {

    }

    /** 脚本被销毁时最后一帧执行完调用此函数 */
    protected onDestroy(): void {

    }
}
```

6. 在【工程内容】-【脚本】中新建脚本，取名为trigger2，并打开进行编辑保存

*脚本示例  ps：对象id需要替换为自己游戏中对应id*

```JavaScript
@Component
export default class trigger2 extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        let trigger = this.gameObject as Trigger;
        trigger.onEnter.add((gameObject: GameObject) => {
            if(gameObject instanceof Character) {
                let chara = gameObject as Character;
                chara.switchToFlying();
            }
        });

         trigger.onLeave.add((gameObject: GameObject) => {
            if(gameObject instanceof Character) {
                let chara = gameObject as Character;
                chara.switchToWalking();
            }
        });

    }

    /**
     * 周期函数 每帧执行
     * 此函数执行需要将this.useUpdate赋值为true
     * @param dt 当前帧与上一帧的延迟 / 秒
     */
    protected onUpdate(dt: number): void {

    }

    /** 脚本被销毁时最后一帧执行完调用此函数 */
    protected onDestroy(): void {

    }
}
```

7. 在【对象管理器】中将trigger脚本挂载到机关对应触发器下，将trigger2脚本挂载到障碍对应触发器下

![](https://cdn.233xyx.com/online/BtKWYrurs9Rx1694487468870.png)

8. 运行看看效果

运行效果

<video controls src="https://cdn.233xyx.com/online/uf5XbqoEX1IU1694487493892.mp4"></video>


日志输出内容

![](https://cdn.233xyx.com/online/BWx9Fj1HLJET1694487468870.png)

# 相关API

[触发器api](https://api-docs.ark.online/classes/Gameplay.Trigger.html)
