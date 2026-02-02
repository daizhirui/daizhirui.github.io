---
layout: article
show_date: false
---

# Performance Improvement for Particle Filter SLAM

In practice, it is sometimes difficult to well-model the motion and the observation because of some unpredictable disturbances. Particle filter is a sampling method to approximate these two models. However, the number of particles is a critical factor that influences the speed and the accuracy of particle filter SLAM. In this project, with the dataset generated with the THOR robot, I implemented the particle filter SLAM and improved its performance. Some modifications are proposed. [Link](/assets/pdfs/particle_filter_slam.pdf)

| Scene 0 | Scene 1 | Scene 2 |
|:-------:|:-------:|:-------:|
|<img src="/assets/images/slam/train0.gif">|<img src="/assets/images/slam/train2.gif">|<img src="/assets/images/slam/train4.gif">|
