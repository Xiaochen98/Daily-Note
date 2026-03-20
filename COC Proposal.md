COC Proposal Draft
Learning-based Navigation and Decision Making for Mobile Robots in Unstructured Environments
1. Introduction

Autonomous navigation in unstructured environments remains a fundamental challenge in robotics. Such environments are characterized by incomplete prior knowledge, irregular geometry, and uncertain traversability, and commonly arise in applications such as search and rescue, planetary exploration, agricultural inspection, and infrastructure monitoring.

Traditional mobile robot navigation systems typically adopt a modular pipeline consisting of Simultaneous Localization and Mapping (SLAM), global path planning, and local control. While these methods have demonstrated strong performance in structured or partially known environments, they primarily focus on motion execution and path optimality. They do not explicitly address the high-level decision problem of where the robot should navigate next in unknown environments.

To address this issue, frontier-based exploration has been widely used as a baseline strategy. Frontier methods select candidate targets on the boundary between known free space and unknown regions. Although effective and computationally simple, frontier selection is usually heuristic, greedy, and myopic. It often relies on handcrafted measures such as frontier size, distance, or estimated information gain, and lacks long-horizon decision-making capability. As a result, it may lead to redundant traversal and suboptimal coverage efficiency.

Recent advances in learning-based methods, especially reinforcement learning (RL), provide a promising path beyond handcrafted heuristics. By learning policies through interaction, robots may acquire more adaptive and efficient exploration strategies under uncertainty. However, existing learning-based approaches often face challenges in stability, sample efficiency, generalization, and integration with practical navigation systems.

In this work, navigation is considered as a hierarchical problem. Low-level motion execution is handled by classical navigation components, while high-level decision making determines where the robot should navigate next under uncertainty. The focus of this proposal is therefore on improving navigation performance through learning-based exploration and goal selection.

2. Problem Statement

This research addresses the following question:

How can a mobile robot learn efficient exploration and decision-making strategies in unstructured environments under uncertainty, beyond traditional frontier-based methods?

3. Research Objectives

The objectives of this research are:

To develop a learning-based exploration framework for mobile robots.

To replace or augment heuristic frontier-based decision making.

To improve exploration efficiency and robustness in unknown environments.

To maintain compatibility with classical navigation stacks such as SLAM and path planners.

4. Literature Review
4.1 Classical Navigation Methods

Classical navigation methods mainly address motion planning and execution under known goals.

Graph-search methods such as A* and Dijkstra are widely used for shortest-path planning on known maps. Sampling-based planners such as Rapidly-exploring Random Trees (RRT) and Probabilistic Roadmaps (PRM) are effective for generating feasible paths in high-dimensional spaces. Variants such as RRT* and PRM* further improve asymptotic optimality. In addition, metaheuristic approaches such as Genetic Algorithms, Particle Swarm Optimization, Ant Colony Optimization, and Simulated Annealing have also been explored for path planning.

These methods work well when the map and goal are known. However, in unstructured or unknown environments, the map is incomplete and the robot must explore while planning. In such cases, purely classical planning methods are insufficient because they do not determine which unexplored region should be visited next.

4.2 Classical Exploration Methods

Classical exploration methods aim to guide the robot into unknown regions and are typically built on top of mapping and planning modules.

Frontier-based exploration is one of the most common baselines. It identifies frontiers as the boundary between known free space and unknown space, and selects the next target using heuristic criteria such as distance, frontier size, or information gain.

Sampling-based exploration generates multiple candidate viewpoints through random or structured sampling and evaluates each candidate based on expected information gain. These methods can better handle narrow passages, but often incur higher computational cost.

Information-theoretic exploration explicitly models uncertainty using metrics such as entropy, and selects actions that balance information gain against motion cost. These approaches are more principled, but are usually computationally expensive and difficult to implement in real time.

Although these methods are effective in many settings, they share several common limitations. They are often heuristic-driven, hand-tuned, and short-sighted. They usually lack long-horizon decision-making capability, which can lead to inefficient coverage and redundant traversal.

4.3 Learning-based Exploration

Learning-based exploration aims to learn a policy that enables a robot to actively select informative regions or targets in unknown environments. Instead of relying solely on handcrafted heuristics, these methods formulate exploration as a sequential decision-making problem, often modeled as a Markov Decision Process (MDP) or a Partially Observable Markov Decision Process (POMDP).

Reinforcement learning is a common approach in this context. The robot learns a policy that maps observations, such as occupancy maps, local sensor data, or exploration states, to actions such as selecting exploration targets, candidate regions, or waypoints. Through interaction with the environment, the policy can optimize long-term rewards and potentially outperform myopic heuristic strategies.

Existing learning-based methods can be broadly divided into two categories.

The first category is end-to-end navigation policies, which directly map raw sensor inputs to control commands. These methods are commonly used for local obstacle avoidance or goal-reaching tasks. While they can show strong reactive behavior, they often lack interpretability and are difficult to integrate with classical navigation systems.

The second category is decision-level policies, which operate on higher-level representations such as occupancy maps, frontier candidates, or spatial features. These approaches focus on selecting where to go next rather than directly controlling robot motion. They are more compatible with existing navigation stacks, but remain less explored in practical robotic exploration systems.

Despite their potential, learning-based exploration methods still face several major challenges. First, RL training can be unstable and sensitive to reward design and hyperparameters. Second, these methods often suffer from low sample efficiency, requiring large amounts of training data. Third, policies trained in simulation may generalize poorly to unseen environments or real-world deployment. Finally, integration with SLAM, path planning, and safety constraints remains a practical engineering challenge.

These limitations suggest that a hybrid approach may be more effective: a framework that uses learning-based policies for high-level exploration decisions while relying on classical navigation components for safe and reliable execution.

5. Literature Gap

Existing navigation methods perform well in motion execution and path optimization under known goals, but they do not address the problem of selecting exploration targets in unknown environments.

Classical exploration methods, especially frontier-based approaches, rely heavily on heuristic rules and are often myopic, leading to inefficient coverage and redundant traversal.

Learning-based methods have shown potential for improving exploration decision making, but most existing studies focus on goal-reaching or obstacle avoidance rather than high-level exploration. In addition, they frequently suffer from instability, low sample efficiency, limited generalization, and weak integration with practical robotic navigation systems.

Therefore, there remains a need for a hybrid framework that enables learning-based decision making for exploration, while leveraging classical navigation components for reliable and safe execution on real robots.

6. Proposed Method
6.1 Overall Idea

This research proposes a learning-based decision-making framework that replaces or augments heuristic frontier selection. The learned policy is responsible for selecting exploration goals under uncertainty, while classical components such as SLAM and path planning ensure geometric consistency, collision avoidance, and reliable motion execution.

The key idea is to separate the problem into two levels:

High-level decision making: selecting which unexplored region or target should be visited next.

Low-level navigation execution: planning and following a safe path to the selected target.

This hybrid design aims to improve exploration efficiency without replacing the well-established strengths of classical planners.

6.2 System Architecture

The proposed system can be summarized as follows:

SLAM → Occupancy Map → Candidate Exploration Targets
↓
Learning-based Policy
↓
Goal Selection
↓
Path Planning (e.g. A*)
↓
Execution

In this architecture, the learning module focuses on which target to choose, while the classical navigation stack focuses on how to reach it safely and reliably.

6.3 State Representation

The policy input may include:

Local or global occupancy grid maps

Robot pose and motion state

Explored versus unexplored region masks

Optional extensions may include:

Frontier distribution maps

Distance maps or cost-to-go maps

Local environmental features related to uncertainty or coverage

6.4 Action Space

The action space can be defined in one of two ways:

Selecting one candidate frontier cluster from a finite set of exploration targets

Outputting a higher-level goal waypoint or exploration direction

The first option is simpler and more compatible with discrete RL methods, while the second offers more flexibility but may require continuous control formulations.

6.5 Reward Design

The reward function is designed to balance exploration effectiveness and motion cost:

𝑟
𝑒
𝑤
𝑎
𝑟
𝑑
=
𝛼
⋅
𝑒
𝑥
𝑝
𝑙
𝑜
𝑟
𝑎
𝑡
𝑖
𝑜
𝑛
_
𝑔
𝑎
𝑖
𝑛
−
𝛽
⋅
𝑡
𝑟
𝑎
𝑣
𝑒
𝑙
_
𝑐
𝑜
𝑠
𝑡
reward=α⋅exploration_gain−β⋅travel_cost

Possible reward terms include:

Increase in explored area or coverage

Penalty for revisiting previously explored regions

Reward for efficient completion in shorter time or with shorter path length

Penalty for invalid or unsafe target choices

6.6 Learning Framework

This study will evaluate reinforcement learning algorithms such as PPO and DQN, depending on the final action-space formulation. The objective is to train a policy that predicts which region should be explored next, rather than directly replacing classical motion planning.

The focus of this work is on improving exploration efficiency rather than path optimality, which is already well addressed by classical planning algorithms.

6.7 Baselines

The proposed method will be compared against representative classical baselines, including:

Frontier-based exploration

Information-gain or information-theoretic exploration strategies

6.8 Evaluation Metrics

The system will be evaluated using the following metrics:

Exploration coverage (%)

Time to completion

Path length

Redundancy or revisit rate

7. Research Platform and Experimental Plan

The proposed framework will be implemented within a ROS-based robotic platform. Training and validation will first be conducted in simulation, where large-scale experimentation can be performed efficiently and safely. The learned policy will then be transferred to a real robot through a sim-to-real process with appropriate calibration, safety checks, and system integration.

This staged workflow is intended to balance research flexibility with practical feasibility, while supporting future deployment in real robotic exploration tasks.

8. Expected Contributions

The expected contributions of this research are as follows:

A learning-based framework for high-level exploration decision making in unstructured environments

A hybrid architecture that combines learning-based goal selection with classical SLAM and planning

Improved exploration efficiency and reduced redundant traversal compared with heuristic baselines

A scalable foundation for future extensions to semantic exploration and dynamic environments

9. Conclusion

This proposal presents a learning-based framework for autonomous exploration and decision making in unstructured environments. By learning a high-level goal-selection policy, the robot can improve exploration efficiency and reduce redundant traversal, while still relying on SLAM and classical planners for safe and reliable execution.

The proposed research is expected to contribute both a practical hybrid navigation framework and a clearer understanding of how learning-based decision making can enhance robotic exploration in unknown environments.
