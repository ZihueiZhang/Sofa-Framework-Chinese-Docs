# Sofa Mouse Manager
Sofa 中创建了一个mouse manager，允许轻松创建和更改与鼠标不同按钮的交互。 注意在操作过程中需要按住**Shift键**。

此面板可通过以下方式使用：*Edit* -> *Mouse Manager*

![image]()

基本交互的介绍如下（有关更多信息，请参见以下部分）：

 - Attach an object to the Mouse 将对象附加到鼠标：单击时，鼠标投射一条射线，并在射线中的一个点和遇到的自由度之间创建一个弹簧（如果在射线附近发现了一个）
 - Fix picked particle 固定拾取的粒子：单击时，鼠标附近碰撞模型的粒子使用刚性弹簧固定。
 - Incise along a path 沿路径切割：按下鼠标，您可以沿着鼠标的路径进行切割。 （仅适用于三角形网格）
 - Perform topological operations 执行拓扑操作：有助于进行一些拓扑更改。 目前只有原始删除可用。
 - Add a spring to suture two points 添加一个弹簧来缝合两点：允许在两点之间创建一个具有空静止长度的弹簧，以便将它们连接起来。
 - Add a Frame to Skinned model 向蒙皮模型添加框架：TODO
 - Save camera’s view points for navigation 保存相机的导航点：TODO
 - Start navigation if camera’s view point have been saved 如果已保存相机的iew，则开始导航：TODO
 
 # Operations available
 有关特定鼠标交互的更多信息。

## Attach an object to the Mouse 将对象附加到鼠标
单击时，鼠标投射一条射线，并在射线中的一个点和遇到的自由度之间创建一个弹簧（如果在射线附近发现了一个自由度DOF） 。
点击 shift 时，会出现碰撞模型（第一个图）,然后在不释放 shift 的情况下单击鼠标按钮将允许移动选定的自由度（第二个图）。 
然后，释放鼠标按钮将使对象回到静止位置（如果机械行为允许）。 
而首先释放 shift 会将对象冻结在当前位置。

## Fix picked particle 修复拾取的粒子
单击时，鼠标附近的碰撞模型的粒子使用刚性弹簧固定。 按下 shift 时，会出现碰撞模型（第一个图），然后单击鼠标按钮将使用刚性弹簧将碰撞模型的粒子固定在鼠标附近。
![image](200px-Fixed_011.png)

## Incise along a path 沿着路径切开
此操作允许模拟三角形网格上的切口。 有两种方法可以进行切口：

通过segment这个方法，第一次点击设置切口的起始位置，第二次点击设置结束位置。 然后将在这些点之间进行切口。 如果您不释放“shift”按钮，则可以继续单击。 切口将在上一个点和这个新点之间进行。 因此，您可以获得连续的切口。 （见图1至图3）
