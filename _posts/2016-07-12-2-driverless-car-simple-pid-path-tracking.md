---
layout: post
title: "2. Driverless Car: Simple PID - Path Tracking (smoothed path)"
tags:
- Driverless Car
- PID controller
---

My RC car's steering angle is 18 degrees maximum. It can't handle sharp turns
very well. One way to address this issue is path-smoothing.

The video below compares sharp turns and smoothed turns. While the car crashes
into the wall when the turns are 90 degrees sharp, it can make it to the goal
position in the smoothed path with relative ease.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Y4gmFlt0NCU" frameborder="0" allowfullscreen></iframe>

Gradient descent is used to smooth the given path in terms of waypoints in the
video. To find out how, please see the [notebook][1]. [Arduino source code][2]
is also available. Note that steering PID derivative coefficient of real RC car
is lowered by one to reduce steering oscillation.

[1]: https://github.com/gokhanettin/driverless-car-notes/tree/v0.1.1
[2]: https://github.com/gokhanettin/driverless-car/tree/v0.1.1
