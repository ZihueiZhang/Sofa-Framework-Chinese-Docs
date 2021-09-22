### Broad Phase Components ###
broad phase 碰撞检测组件在碰撞管道中执行。

### Introduction ###
在 SOFA 中，碰撞检测通常涉及复杂的网格（例如三角面片集合）。 为了获得准确的碰撞响应，碰撞检测会检测哪些碰撞元素对相交。

原始的方法是测试每一对碰撞元素。 测试的数量取决于对象的数量，以及每个对象中碰撞元素的数量。 在性能方面的原因，由于其二次方的复杂性，基本不会选择这种方法。

取而代之的，碰撞检测将分为两部分：

1. broad phase 碰撞检测
2. narow phase 碰撞检测

### The Broad Phase ###
在 SOFA 中，broad phase 的作用通常是修剪最大数量的不相交的碰撞模型对。 
考虑到n个物体碰撞模型（通常每个物体有多个碰撞模型），有n*(n-1)/2（没有自碰撞）至n^2/2（所有碰撞都是自碰撞）对碰撞模型。

broad phase输出是一组可能相交的碰撞模型对。 在这个阶段，不知道这些对是否真的相交。 不知道哪些碰撞元素（即点/线/三角形）与哪些碰撞元素相交。 对列表作为narrow phase撞检测的输入提供。

### The Implementation ###
在开始广泛阶段之前，必须列出场景中的所有碰撞模型。 这是在函数 ``void PipelineImpl::computeCollisionDetection()``中完成的：
````
std::vector<CollisionModel*> collisionModels;
root->getTreeObjects<CollisionModel> (&collisionModels);
````
然后，broad phase碰撞检测在 3 个函数中执行：
````
void BroadPhaseDetection::beginBroadPhase()
````
````
void BroadPhaseDetection::addCollisionModels(const sofa::helper::vector<core::CollisionModel *>& v)
````
````
void BroadPhaseDetection::endBroadPhase()
````
函数 ``addCollisionModels`` 被场景中列表上的所有碰撞模型调用。 在内部，这个函数只是一个调用以下函数的循环：
````
void BroadPhaseDetection::addCollisionModel(core::CollisionModel *cm)
````
这 3 个函数（```beginBroadPhase```、```addCollisionModel``` 和 ```endBroadPhase```）的实现定义了 broad phase 的行为。 这是算法实现的地方。 要实现新的 broad phase 算法，开发人员可能会从 ```BroadPhaseDetection``` 派生一个类并覆盖提到的 3 个函数。

执行 broad phase 后，潜在碰撞对列表存储在
````
sofa::helper::vector< CollisionModelPair > BroadPhaseDetection::cmPairs;
````
最后，碰撞管道将此列表提供给 narrow phase 碰撞检测。

### Examples of Components ###
 - BruteForceBroadPhase
 - ParallelBruteForceBroadPhase (plugin MultiThreading)
 - BruteForceDetection
 - THMPGSpatialHashing (plugin THMPGSpatialHashing)
 - BulletCollisionDetection (plugin BulletCollisionDetection)
 
