### Collision Pipelines ###
对象之间的碰撞分为几个阶段，每个阶段都在不同的组件中实现。 每个阶段都由一个碰撞管道调度。 碰撞管道在动画循环中执行。\

### The Steps ###
碰撞管道遵循三个步骤：

1. 碰撞重置
2. 碰撞检测
3. 碰撞响应

### Implementation  ###

碰撞管道从动画循环通过执行 CollisionVisitor::processCollisionPipeline 中管道的 3 个步骤的 CollisionVisitor 调用。

访问者执行以下函数，每个函数对应管道的一个步骤：

````
/// Remove collision response from last step
void Pipeline::computeCollisionReset()
````

````
/// Detect new collisions. Note that this step must not modify the simulation graph
void Pipeline::computeCollisionDetection()
````

````
/// Add collision response in the simulation graph
void Pipeline::computeCollisionResponse()
````

这些函数中的每一个都会调用一个delegate，在流水线中可用：
````
/// Remove collision response from last step
void doCollisionReset() override;
````
````
/// Detect new collisions. Note that this step must not modify the simulation graph
void doCollisionDetection(const sofa::helper::vector<core::CollisionModel*>& collisionModels) override;
````
````
/// Add collision response in the simulation graph
void doCollisionResponse() override;
````
3个delegate函数描述了 3 个不同的步骤，并且通常在派生类中被覆盖。 请参阅 DefaultPipeline 中的示例。

注意：在某些情况下，动画循环通过 3 个专用Visitor（CollisionResetVisitor、CollisionDetectionVisitor 和 CollisionResponseVisitor）手动调用这 3 个步骤。 这些访问者中的每一个只执行一个步骤（而不是 3 个）。 这是为了避免多线程环境中的竞争条件。

