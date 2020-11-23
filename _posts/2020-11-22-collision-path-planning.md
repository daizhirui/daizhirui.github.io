---
layout: article
show_date: false
---

# Collision Detection and Path Planning

In the field of robotics, how to determine the optimal path to the destination is a critical problem
because an optimal path can help to reduce energy consumption of the robot, bring down the risk of
damage and so on. Label correcting algorithm is one of many algorithm designed for path planning.
It can guarantee an optimal path. However, in practice, not only energy but also time is precious.
In complicated cases, LCA and other methods based on it is still too slow to balance the time
efficiency and other metrics. Therefore, some faster methods, like Rapid-exploring Random Tree,
are proposed to boost the speed by providing a sub-optimal path.
In this project, I implemented six algorithms, collision detection based on Minkowski difference
and winding number, A*, RRT, RRT*, RRT-Connect and RRT*-Connect, to compare them with
seven different environments and discussed their advantages and disadvantages. [Link](/assets/pdfs/path_planning_comp.pdf)

| Algorithm | Result | Time (s) |
|:--:|:--:|:--:|
| A* | <img src="/assets/images/path-planning/room-astar.png" width=800> | 3.85 |
| RRT | <img src="/assets/images/path-planning/room-rrt.png" width=800> | 0.47 |
| RRT* | <img src="/assets/images/path-planning/room-rrt.png" width=800> | 2.19 |
| RRT-Connect | <img src="/assets/images/path-planning/room-rrt-connect.png" width=800> | 0.09 |
| RRT-Connect* | <img src="/assets/images/path-planning/room-rrtstar-connect.png" width=800> | 1.21 |
