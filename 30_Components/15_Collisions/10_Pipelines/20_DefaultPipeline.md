### Collision Pipelines: DefaultPipeline ###

DefaultPipeline 是一个碰撞管道。 它执行与碰撞相关的步骤，主要是碰撞检测和碰撞响应。

动画循环执行管道的 3 个步骤（参见碰撞管道文档）。

### Interaction with Other Components ###

DefaultPipeline 需要在同一上下文中定义的其他组件：
 - An intersection method（例如 MinProximityIntersection、LocalMinDistance）
 - A broad phase detection（例如 BruteForceBroadPhase）
 - A narrow phase detection（例如 BVHNarrowPhase）
 - contact manager（例如 DefaultContactManager）
 - [可选] A group manager（例如 DefaultCollisionGroupManager）
 
如果未提供交集方法，则会创建一个默认的 DiscreteIntersection 组件并将其添加到场景图中，并向用户发出警告，并用作交集方法。

### Pipeline ###
pipline的三个步骤：

#### Collision Reset ####
这一步主要是清除前一时间步计算的数据。 它在函数中实现
````
void DefaultPipeline::doCollisionReset()
````
1. 所有contact manager提供的contacts被清除
2. group manager清理所有的group

### Collision Detection ###
1. 对于所有碰撞模型，计算其边界树。 树的深度由 DefaultPipeline 中的数据定义。 如果broad phase或narrow phase不需要deep tree（needsDeepBoundingTree()），则树是最小的。 在任何情况下，都会创建一个 CubeCollisionModel 并将其链接到每个碰撞模型，以用作 broad phase 中的 Axis-Aligned Bounding Box。 构建树会考虑碰撞检测是否连续。 这在intersection中被定义的。
2. 执行broad phase碰撞检测
3. 执行narrow phase碰撞检测
碰撞检测在以下函数中实现：
````
void DefaultPipeline::doCollisionDetection()
````

### Collision Response ###
1. 根据碰撞检测的结果在Contact Manager中创建Contacts
2. 为所有未模拟的相交碰撞模型对创建响应
3. 为其余的交叉碰撞模型对（模拟的模型）创建响应。 如果有的话，这一步可以由group manager执行。

响应的创建取决于contact manager中定义的contact类型。

碰撞响应在以下函数中实现:
````
void DefaultPipeline::doCollisionResponse(const helper::vector<core::CollisionModel*>& collisionModels)
````

### Example of Usage ###
该组件以 XML 格式使用如下：
````
<FreeMotionAnimationLoop />
<DefaultPipeline depth="15" verbose="0" draw="0" />
<BruteForceBroadPhase name="N2" />
<BVHNarrowPhase/>
<MinProximityIntersection name="Proximity" alarmDistance="1.5" contactDistance="1" />
<DefaultContactManager name="Response" response="FrictionContact" />
````
