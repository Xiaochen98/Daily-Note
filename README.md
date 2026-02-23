# Daily-Note

0220：
我修复了机器人不能走直线的问题，通过旋转电位极调整舵机的初始位置让机器人在3m内误差3cm

学习了机器人手柄的使用方式。


0221：
我修复了机器人的时间问题，由于没有RTC时钟，机器人的时间常常错误，错误的时间有时会导致ros2的某些程序出错。我建议以后的工作流程可以是在机器人需要演示不能出错的时候，先用手机蓝牙给机器人共享网络，校准时间，我也可以问一问wheeltec官方对这个问题的解决方法。

我修复了机器人雷达的问题，原本雷达dirver会在启动gmapping/nav2的时候被拉起，但是由于雷达需要一段时间才能开始正常输出，有的依赖雷达输出的节点会卡死，一般10次会有5次卡死。修改launch file后，雷达能够稳定的运行了。
同时我还修改了driver的输出，原本是同时输出/pointcloudraw和/laserscan，修改后只输出/pointcloudraw.然后用另一个节点转化为/laserscan，避免了同时有多个源发送信息的问题。

今天主要了解了下导航的算法
基本上导航算法可以分为两类：
非启发式的：
其中最经典的算法是Dijkstra算法，类似广度优先算法BFS，但是会考虑每条路的权重（距离）
启发式的：
A*: A*就是Dijkstra算法加上一个启发函数，一般可以使用笛卡尔距离或者是曼哈顿距离。

Nav2 的 Global Planner 基于图搜索 / 采样 / 混合状态空间算法，常见插件包括：
NavFn（Dijkstra / A* 类）
Smac Planner（Hybrid-A* / State lattice，适合 Ackermann）
Theta*（任意角度路径）

在Global Planning之后有Local Control 也就是轨迹跟踪
NAV2有DWB， Pure Pursuit， MPPI等算法用于实际控制机器人的运动。我的机器人使用的是MPPI算法，由于参数过于激进，导致机器人在小空间里表现不佳，调整了速度，加速度和std这几个参数后机器人的表现更稳定了一些。但是需要更多的测试来验证鲁棒性。


0222：
今天将机器人底盘的程序上传到了github，以后修改程序后不用担心丢失之前的记录了，上传过程花了很久因为机器默认使用的是TCP 22端口。上传速度只有几KB。排查了很久才发现原因。修改.ssh里的config强制使用443端口立马就成功上传了。

之后还修复了无法在jetson里使用浏览器的问题，虽然我不需要在jetson里用浏览器但是没有我就不舒服。我了解到在ubuntu里面 我们可以用apt，snap， faltpak来安装软件，jetson里的snap是被裁减过的，不好用，最后我用faltpak安装了firefox，没有装chrome因为chrome没有arm版本。

下一步：
我需要了解非结构环境下机器人导航这个大话题下大家一般研究什么。我准备搜索然后下载csv给gpt
我需要了解更多新的导航算法和原理
我需要知道机器人实现NAV2总共用了哪些文件。

0223:
阅读文献：A comprehensive review of path planning algorithms for autonomous navigation
作者Sangeeth Venu发表于Results in Engineering
链接https://www.sciencedirect.com/science/article/pii/S2590123025038034

经典（Classical）方法	
  A* 算法	图搜索最短路径算法，启发式搜索典型方法。
	Dijkstra 算法	无启发式的最短路径求解方法，适用于加权图。
	迪格拉/Bellman–Ford	经典图搜索路径规划方法（可能在综述中提及作为传统基础）。
  采样/随机方法（Sampling-based）	迅速扩展随机树 RRT	使用随机采样扩展树结构进行路径探索。
	概率路网 PRM	构建自由空间概率路网，再进行图搜索。
	RRT*, PRM* 等变体	改进的采样算法，追求最优路径等。
  
元启发式（Metaheuristic）方法	
  遗传算法 GA	模拟自然选择优化路径。
	粒子群优化 PSO	基于群体智能搜索最优路径。
	蚁群优化 ACO	模拟蚂蚁行为寻找最短路径。
	模拟退火 SA	模拟热力学退火过程搜索全局解。
	其他自然启发算法（如差分进化 DE、萤火虫算法等）	常见于路径规划优化问题。
  
人工智能／机器学习方法	
  强化学习 RL	通过奖励学习最优路径策略。
	深度强化学习 Deep RL	利用深度神经网络学习复杂导航策略。
	人工神经网络 NN	使用网络学习环境特点进行规划。
	混合 AI–经典方法（如 PRM-RL）	将经典规划与 RL 结合提高性能。
  
混合与其他方法
  Hybrid A* / 图与采样结合	结合图搜索和采样优化路径。
	几何方法（Voronoi 图、细胞分解等）	基于环境几何特征进行规划。
	局部避障 + 全局规划混合策略	全局规划配合局部动态避障机制。


阅读文献：Deep Reinforcement Learning Based Mobile Robot Navigation: A Review
作者：Kai Zhu
链接https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9409758

Sider AI对非结构环境机器人导航的一个总结：
https://community.sider.ai/deep-research/jh1e6lP79Pb0jIyHAdHwFN?view=1

