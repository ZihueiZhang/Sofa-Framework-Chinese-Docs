## Monitor组件
名为 Monitor (sofa::component::misc::Monitor) 的sofa组件可以帮助可视化、监控或导出某些属性。

### Quick overview of Minitor posibilities
使用此组件，您可以直接在 GUI 中查看所选粒子的位置、轨迹、速度、力或将其保存到文件中（例如保存成可使用 Gnuplot 读取的数据）。 可以通过运行 Sofa/examples/Components/misc/Monitor.scn 场景来了解此组件。

### 使用监视器组件
要监视属性，您必须在场景中添加一个监视器组件。 下面是示例场景的一个 Monitor 的一段代码的选择：

````
<Monitor template="Vec3d" name="velocities_8-16-24" listening="1" indices="8 16 24" showPositions="0" PositionsColor="1 1 0 1" showVelocities="1" VelocitiesColor="1 1 0 1" ForcesColor="1 1 0 1" showMinThreshold="0.01" TrajectoriesPrecision="0.1" TrajectoriesColor="1 1 0 1" sizeFactor="1" />

````

也可以使用Modeler将组件添加到您的场景中，在搜索表单中查找Monitor。 必需的是：要使Monitor工作，您必须在 GUI 中指定 listen="1" 或将监听设置为 true。 您必须选择要监视或显示的属性。

 - 如果要可视化位置，则必须选择 showPositions（单击组件 GUI 中 Visualization 选项卡中的相应字段或在场景描述文件中添加 showPositions="1"）。
 - 然后，可以使用 PositionsColor 选择要应用position可视化的颜色，并在需要时使用 sizeFactor 选择可视化的大小。
 - 您必须使用索引字段选择要监视的dofs：``indices ="8 16 24"``
 - 您可以选择使用 ExportPositions 导出dofs的位置。 这些位置将被导出到一个文件中，该文件遵循以下规则命名： Sofa 目录中的 your_monitor_component_name+_x.txt。


|          | Field         |	XML                |	example	                    |Default Comments|
| ---      | ---           |---                  |----                          |---             |
|          |indices	       |""	                 |indices="0 2 4"	              |select the dofs to be monitored|
|Positions |showPositions	 |false	               |showPositions="true"	        |to monitor positions|
|Positions |PositionsColor |(1.0, 1.0, 0.0, 1.0) |PositionsColor="1 1 0 1"	    |to visualize the positions as some yellow points|
|Positions |ExportPositions|false                |ExportPositions="true"	      |positions will be exported to ${component_name}_x.txt|
|Velocities|showVelocities |false                |showVelocities="true"	        |to monitor velocities|
|Velocities|VelocitiesColor|(1.0, 1.0, 0.0, 1.0) |VelocitiesColor="1 0 1 1"	    |to visualize the velocities as some purple arrows|
|Velocities|ExportVelocities|false               |ExportVelocities="true"	      |velocities will be exported to ${component_name}_v.txt|
|Forces	   |showForces     |false                |showForces="true"	            |to monitor forces|
|Forces	   |ForcesColor	   |(1.0, 1.0, 0.0, 1.0) |ForcesColor="1 0 0 1"	        |to visualize the forces as some red arrows|
|Forces	   |ExportForces	 |false                |ExportForces="true"	          |forces will be exported to ${component_name}_f.txt|
|Trajectories|showTrajectories|	false            |showTrajectories="true"	      |to visualize the trajectories|
|Trajectories|TrajectoriesColor|(1.0, 1.0, 0.0, 1.0)|TrajectoriesColor="1 0 1 1"|to get nice purple trajectories represented as lines|
|Trajectories|TrajectoriesPrecision|	1.0	       |TrajectoriesPrecision="0.1"	  |to specify the timestep between two positions saving to reconstruct the trajectory|
|          |sizeFactor       |1.0	               |sizeFactor="true"	            |to change the size of the drawing (points, arrows or lines)|
|          |showMinThreshold|	0.01	             |showMinThreshold="0.01"	      |under this value, vectors are not represented|

### Visualize the result
要读取生成的文件，您可以使用 Gnuplot。 例如，在 Gnuplot 中，您可以运行以下命令来渲染导出文件的曲线：
````
splot "monitor-displacement-faceNode_x.txt"
````

