组件实现了大部分的仿真方法。我们可以粗略地区分两类组件:  
**Property组件**实现一个对象的物理属性的一个方面，比如它的质量、刚度、附件。它们与给定的模拟对象相关联，并且c++类通常按照对象的自由度(DOF)类型模板化。一个对象最重要的组成部分是它的MechanicalState，它包含状态向量:位置、速度和辅助向量。  
**Control组件**实现高级算法，如时间集成和碰撞检测。它们中的大多数都没有附加到给定的对象。它们控制其范围内的所有对象(它们在场景中的子图)，使用遍历场景的visitors来应用虚函数。

#### Base functions  
一个SOFA组件是一个来自 `sofa::core::objectmodel::BaseObject` 的类。通过这种方式，提供了几个虚方法，为了正确配置组件，必须知道这些虚方法。  
**`init()` and `bwdInit()`**  
当SOFA加载一个模拟器时，它会使用C++或直接使用XML/Python创建SOFA组件（和默认构造函数）。在这个阶段，必须初始化**Data**。Data组件可以从`sofa::core::objectmodel::Data`中找到。这个utility class的目的是存储组件的所有参数，并以这种方式处理输入（例如从XML/Python文件中参数化组件）和输出（在时刻T保存组件的配置）。需要保存在组件中的所有内容都必须通过Data保存在内存中。
初始化Data必须要经过的步骤：  
````  
//Previous declaration of the Data，数据声明
Data<bool> d_isEnabled;

//In Component constructor，在组件构造函数中
MyComponent():
d_isEnabled(initData(&d_isEnabled, true, "isEnabled", "Boolean indicating if the component is enable"))) 
//ptr to the data, default value, name used for the parameter (the same that will appear later in the XML/Python file), description of the parameter (its purpose)
// Data指针，默认值，参数名称（和XML/Python文件中的一致），参数描述（参数用途）
{
};
````  
一旦场景中所有的SPFA组件被创建后，我们启动一个Visitor来初始化组件。基本上，一个`Visitor`从一个node开始（对于`InitVisitor`，我们从根开始），执行几个特定的操作，从上→下，然后执行另一组操作，从下→上。每次我们初始化一个有两个方法的场景时，都会调用`InitVisitor`来做translate：  
````  
void init(); //call during Top->Down traversal，从上到下遍历时调用
void bwdInit(); //call during Bottom->Up traversal，从下到上遍历时调用
````  
`init()`在图的每个组件中被调用（从上到下），进行初始化。`bwdInit()`在节点的所有children都被初始化之后被调用（从下到上）。子节点的所有组件都调用了`init()`和`bwdInit()`方法。这两个方法都是来自**BaseObject**的虚函数，可以在任何组件中重写。

**reinit()**  
当你在SOFA的GUI中编辑组件时，`reinit()`方法会自动调用`reinit()`：  
````
void reinit();
````  
**cleanup() and reset()**  
每次你想重置场景时都会调用这些方法：首先调用cleanup，然后调用reset。  
 - 在cleanup中，你必须删除你可能已经添加到场景中的所组件：如果在场景中，你有一些碰撞，并且你创造了接触组件，或碰撞响应组件，那么`cleanup()`是一个remove他们的好方法。
 - 在reset中，必须将组件的所有Datas和内部值设置为默认值。  
 ````
void cleanup();
void reset();
 ````
**draw()**  
所有SOFA组件都有一个`draw()`方法，因此不需要从VisualModel基本组件派生来显示调试信息。它是在模拟结束时直接由GUI调用的。目前，只有一个线程同时运行模拟和可视化。接下来，希望将这些进程分离到两个不同的线程中，这样可视化的频率就不再依赖于模拟的频率（通常要慢得多）。  
````
void draw();
````
**getContext()**  
每个SOFA组件都要一个上下文。通过将此上下文转换为 simulation::Node* ,您可以设法获得包含组件的节点。Node是一个非常有用的组件，因为您可以从它们启动Visitors，或者快速获得关于节点内容的信息。然而，我们坚持这样一个事实，即不建议通过上下文访问其他组件，而是通过创建Link/DataLink。  
````
sofa::core::objectmodel::BaseContext* getContext();
````
Example:
````
simulation::Node* currentNode = static_cast<simulation::Node*>(myComponent->getContext());
````  
**handleEvent( Event* )**
每个SOFA组件都从**BaseObject**中继承一个Data **f_listening**。如果 **f_listening**是true，那么每次将一个Event发送到包含组件的节点时，将调用此方法。通过这种方式，您可以在触发事件时执行特定的操作。  
````
void handleEvent( Event* );
````  
Example:  
````
void handleEvent ( core::objectmodel::Event* ev )
{
  if ( dynamic_cast ( ev ) )
  {
    // Do some operation when the collision detection ends.
  }
}
````  
你可以使用Trace of Visitor来了解触发事件的时间和位置。最常用的SOFA events有：  
 - AnimateBeginEvent
 - AnimateEndEvent
 - CollisionBeginEvent
 - CollisionEndEvent
 - ToPologyChangeEvent
 - UpdateMappingEndEvent  
更多[Event](https://www.sofa-framework.org/community/doc/programming-with-sofa/api-overview/events-in-sofa/)信息。  
