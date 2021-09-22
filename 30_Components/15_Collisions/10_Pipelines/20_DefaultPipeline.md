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
