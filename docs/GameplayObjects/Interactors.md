::: info
**阅读本文预计10分钟**
::: 

# 交互物

> 【本地资源库】中的【游戏功能对象】栏

【交互物】是一个给场景内物体提供与角色交互能力的逻辑对象，在编辑环境中可见，在运行环境中不可见

![](https://cdn.233xyx.com/online/AU7X8GG6A9OG1694485510033.PNG)

在角色与【交互物】交互时，角色会切换成指定的交互姿势，移动到交互位置并焊接至【交互物】上。处于交互中的角色不再受输入控制，转而通过控制【交互物】间接的控制与它交互的角色。交互行为会提供关键节点的委托事件便于用户执行对应的游戏逻辑。

::: tip
一个交互物在同一时刻只能与一个角色对象进行交互，当交互物已经处于交互状态时其他角色无法与其发生交互。如果同时发起交互请求那么先到的角色可以成功交互。
::: 


# 交互物属性

## 基本属性

【交互物】的**位置**控制 **交互行为发生的位置** ，**朝向**控制 **交互行为发生时角色面对的方向** ，可通过【属性面板】中的【变换】调节。

## 交互插槽

> 【交互物】的【属性设置】栏

【交互插槽】指的是与【交互物 **】** 发生交互行为的**角色**身上的 **对应部位** 。

该属性可读可写，可以通过属性面板调节以及调用api设置。当交互行为发生时，角色插槽就会与【交互物】中心重合，并将角色焊接在交互物上。

1. 属性面板调节

通过下拉菜单进行选择

![](https://cdn.233xyx.com/online/FhSTsSwvJEDA1694425255519.PNG)

*插槽枚举*

| 头发     | 脸部   | 头部左侧 | 头部右侧 | 眼镜     | 眼睛     |
| ---------- | -------- | ---------- | ---------- | ---------- | ---------- |
| 面部装饰 | 嘴部   | 左肩     | 右肩     | 左手手套 | 右手手套 |
| 背部装饰 | 左背   | 右背     | 左手     | 右手     | 左脚     |
| 右脚     | 臀部   | 头顶光圈 | 头顶标题 | 聊天框   | 根节点   |
| 左手肘   | 右手肘 | 左大腿根 | 右大腿根 | 左膝盖   | 右膝盖   |

2. 调用api设置

获取【交互物】对象后，通过修改其**slot**属性设置【交互物】的【交互插槽】

```TypeScript
// 获取脚本挂载的对象并以【交互物】对象进行接收
let chair = this.gameObject as Interactor;
// 将交互插槽修改为臀部
chair.slot=HumanoidSlotType.Buttocks;
```

## 交互动画

> 【交互物】的【属性设置】栏

【交互动画】指的是与【交互物】发生交互行为时**角色**的 **对应姿势** 。在【本地资源库】中存有【交互物】使用的动画资源。

该属性可读可写，可以通过属性面板调节以及调用api读取或设置，但是在交互过程中修改该值并不会影响角色当前行为。

1. 属性面板调节

将资源库中对应动画拖拽至【交互动画】框中

![](https://cdn.233xyx.com/online/uwmDa4fqwlwg1694425255520.PNG)

2. 调用api设置

获取【交互物】对象后，通过其**animationId **属性读取或设置【交互物】的【交互动画】

```TypeScript
// 获取脚本挂载的对象并以【交互物】对象进行接收
let chair = this.gameObject as Interactor;
// 将交互动画修改为 29741：坐下
 chair.animationId = "29741";
```

::: tip
由于交互动画属于一种动画资源，所以在使用【交互物】之前需要保证资源加载完成。可以通过预加载或者优先加载的方式来提前加载资源。如果资源没准备好，交互动画可能无法展现。
::: 

## 交互状态

【交互物】具有两种状态：”交互中“和“空闲”，两种状态互斥。

该属性可读不可写，状态由**occupied**表示，是一个布尔类型的值：true代表"交互中"，false表示”空闲“。游戏中通常会实时获取【交互物】的状态来执行对应的游戏逻辑。

*代码示例*

```TypeScript
//获取脚本挂载的对象并以【交互物】对象进行接收
let chair = this.gameObject as Interactor;
//获取交互物状态并打印
console.log("交互物当前状态：" + chair.occupied);
```

## 交互中的角色

与【交互物】正在交互的角色可以通过`getCurrentCharacter()`去获取。

该函数返回一个角色对象，如果【交互物】当前并未与角色交互，那么将会返回空值。

*代码示例*

```TypeScript
//获取脚本挂载的对象并以【交互物】对象进行接收
let chair = this.gameObject as Gameplay.Interactor;
//获取交互中的角色并打印角色名字
 console.log("交互物当前交互角色：" + chair.getCurrentCharacter() ? chair.getCurrentCharacter().displayName  : "无");
```

# 交互物使用

## 交互物创建方式

资源拖拽创建：从【本地资源库】中的【游戏功能对象】栏将【交互物】拖入【场景】或者【对象管理器】来创建对象。

通过脚本创建：具体方式见 [游戏对象的创建与获取](https://meta.feishu.cn/docx/LnC0dsCrkoXTPZxEoywcvUf7nBg)

## 交互控制

交互物导入后，需要交互控制才能使交互行为发生。交互控制主要分为两个类型，进入交互以及退出交互。

### 进入交互

通过`enter`接口与交互物开始交互。`enter`接口要求必须传入一个角色对象，同时还有2个可选参数：交互插槽和交互动画。如果不传入可选参数那么会使用【交互物】自身属性来执行交互行为。执行具体的交互行为前，交互物会记录当前的角色坐标，用于退出交互时使用。

### 退出交互

通过`leave`接口退出与【交互物】的交互。`leave`接口有3个可选参数：退出坐标、退出朝向和退出动画。你可以指定角色退出交互后的位置和动画。如果不传入参数，那么角色将会刷新到以交互前的位置并朝向交互物的正前方，应用角色的默认姿态。

*示例代码*

```TypeScript
// 获取脚本挂载的对象并以【交互物】对象进行接收
let chair = this.gameObject as Interactor；
//识别客户端行为
if(SystemUtil.isClient()) {

   Player.asyncGetLocalPlayer().then((player) => {
     let myself = player.character;
//当按下按键1时，进入交互状态
     InputUtil.onKeyDown(Keys.One, () => {
         chair.enter(myself)
   });
//当按下按键2时，退出交互状态
     InputUtil.onKeyDown(Keys.Two, () => {
         chair.leave()
   });
 });
}
```

## 交互事件

【交互物】有两个事件：开始交互完成事件和退出交互完成事件。

获取到【交互物】对象后。我们可以通过这两个事件添加委托执行的逻辑。每当交互行为进行到对应阶段后，就会触发对应的事件并执行委托的逻辑。

将下列示例代码添加到”进入交互&退出交互“示例中获取对象后：示例代码在该【交互物】对象的开始交互完成事件和退出交互完成事件中各添加一个函数：打印一行信息。

*示例代码*

```TypeScript
//开始交互完成事件
chair.onEnter.add(() => {    
console.log("玩家开始交互");
});

//退出交互完成事件
chair.onLeave.add(() => {
console.log("玩家退出交互");
});
```

![](https://cdn.233xyx.com/online/R8TRNwN2PdGg1694425255520.png)

# 案例：制作一把可以坐的椅子

1. 打开编辑器，创建一个新的空项目，打开项目
2. 在【资源库】-【游戏功能对象】中找到【交互物】，将其拖入场景中
   ![](https://cdn.233xyx.com/online/RkS5Hw4TQbqQ1694425255520.png)
3. 在【资源库】-【美术对象】中找到椅子（可以利用资源搜索栏搜索），选取一个喜欢的拖入场景中

![](https://cdn.233xyx.com/online/HjDpKib4bRwF1694425255519.png)

4. 在【对象管理器】中将交互物拖拽至椅子下（将交互物设置为椅子的子对象），并将交互物的相对坐标置为（0,0,0），并调整z坐标至合适位置，使得交互物位于交互发生的位置（人物坐下的位置），并旋转角度至角色正确面向方向

![](https://cdn.233xyx.com/online/TKEalZePHQWN1694425255519.png)

5. 在【资源库】-【美术对象】-【动画】中找到合适的资源，拖拽到交互物的属性面板中的【交互动画】框中

![](https://cdn.233xyx.com/online/LoXe0OTjY8u01694425255519.png)

6. 在工程内容中新建脚本，取名为chairInteractor，双击打开脚本进行编辑

*脚本示例*

```JavaScript
@Component
export default class chairInteractor extends Script {

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected onStart(): void {
        let chair = this.gameObject as Interactor;
        //识别客户端行为
    if(SystemUtil.isClient()) {

    Player.asyncGetLocalPlayer().then((player) => {
      let myself = player.character;
 //当按下按键1时，进入交互状态
      InputUtil.onKeyDown(Keys.One, () => {
          chair.enter(myself);
    });
 //当按下按键2时，退出交互状态
      InputUtil.onKeyDown(Keys.Two, () => {
        chair.leave();
        //获取交互物状态并打印
    });
//当按下按键3时，修改交互插槽属性，改变交互位置
    InputUtil.onKeyDown(Keys.Three,()=>{
        chair.slot=HumanoidSlotType.RightFoot;
    })
//当按下按键4时，修改交互动画
    InputUtil.onKeyDown(Keys.Four,()=>{
        chair.animationId="182947"

    })
//当按下按键5时，输出交互物状态以及交互对象
    InputUtil.onKeyDown(Keys.Five,()=>{
         //获取交互物状态并打印
         console.log("交互物当前状态：" + chair.occupied);
         //获取交互中的角色并打印角色名字
         console.log("交互物当前交互角色：" + chair.getCurrentCharacter() ? chair.getCurrentCharacter().displayName : "无");
       })
  });
 }
     //开始交互完成事件
     chair.onEnter.add(() => {    
     console.log("玩家开始交互");
    });  

    //退出交互完成事件
     chair.onLeave.add(() => {
     console.log("玩家退出交互");
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

7. 编辑完成后，ctrl+s保存脚本，回到编辑器中，将脚本拖拽挂载至交互物下

![](https://cdn.233xyx.com/online/bGXHGpdt0g2J1694425255519.png)

8. 运行看看效果

运行效果

<video controls src="https://cdn.233xyx.com/online/gyvC42JcWtdx1694485969650.mp4"></video>


日志输出内容

![](https://cdn.233xyx.com/online/S5r4YJHgktuQ1694425255520.png)

# 相关API

[交互物API](https://api-docs.ark.online/classes/Gameplay.Interactor.html)
