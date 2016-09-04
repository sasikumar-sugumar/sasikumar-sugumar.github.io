---
layout: post
title: "3. Driverless Car: Simple PID - Path Tracking (variable desired speed)"
tags:
- Driverless Car
- PID controller
---

It is clear that when we properly slow down the speed before taking a turn, we
lower the risk of driving out of the path. To do that we have to be able to
change desired speed, and a means of speed recommendation.

Before implementing variable desired speed, I noticed car length and
displacement per shaft revolution was measured wrong. Having corrected them, I
implemented a speed recommender. Each waypoint now has a desired speed, and for
the time being, the speed recommender basically recommends these speeds as the
car moves from one waypoint to the next. The speed recommender can be further
extended by means of different sensors such as sonar, laser, camera, etc.

I also increased the steering PID loop frequency from 20 Hz to 40 Hz. Note that
steering PID derivative coefficient in real car slightly differs from the
simulation. This reduces the noise in steering.

<iframe width="560" height="315" src="https://www.youtube.com/embed/70_gBYIDxqU" frameborder="0" allowfullscreen></iframe>

To find out more how the car works see the [notebook][1]. If you want to build
your own self-driving RC car see the [source code][2].

[1]: https://github.com/gokhanettin/driverless-car-notes/tree/v0.1.2
[2]: https://github.com/gokhanettin/driverless-car/tree/v0.1.2
