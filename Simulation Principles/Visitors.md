#Visitors

在仿真的不同步骤（初始化、系统组装、求解、可视化）中，需要从所有图节点恢复信息。 SOFA 依赖于一种隐式机制：the **Visitors**。您可以在 SofaSimulation 包中找到抽象的 Visitor 类。

访问者自顶向下和自底向上遍历场景，并在每个图节点遍历时调用相应的虚函数。因此，访问者被用来通过调用相关的虚拟功能来触发动作（例如动画模拟、累积力）。对simulated objects的算法操作是通过派生Visitor类并重载其虚函数 processNodeTopDown() 和 processNodeBottomUp() 来实现的。

这种方法对组件隐藏了场景结构（父、子），以获得更大的实现灵活性和对执行模型的更好控制。此外，可以独立于每个节点执行的机械计算应用各种并行策略。数据结构实际上从严格的层次结构扩展到有向无环图，以处理更一般的运动依赖关系。除非已经遍历了当前节点的所有父节点，否则自顶向下的节点遍历将被修剪，因此只有在遍历所有父节点后，才会遍历具有多个父节点的节点。自下而上的遍历以相反的顺序进行。

例子：

 - 在 AnimationLoop 级别，visitors用于例如触发模拟步骤 (AnimateVisitor)、更新上下文 (UpdateSimulationContextVisitor) 和更新映射 (UpdateMappingVisitor)。

 - 在 ODESolver 级别，visitors允许通过抽象函数构建线性矩阵系统。例如，右侧向量 b 的计算由 MechanicalComputeForceVisitor 触发，累积力用于计算施加在我们对象上的所有力（内部或外部）。然后求解器触发关联*Visitor*，action通过graph传播，并在每个力和映射节点调用适当的（自下而上）方法。所有能够计算力的组件都会累积它们的贡献。该信息最终被收集在 MechanicalObject 中，求解器将使用该“力”矢量来求解数学系统。

![image](./images/Images-tuto.0010.jpg)

# Sequence diagram
下面是SOFA simulatio
![image](./diagram-sequence-detailed.png)
