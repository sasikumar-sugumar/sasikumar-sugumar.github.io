---
layout: post
title: "1. Driverless Car: Simple PID - Path Tracking"
tags:
- Driverless Car
- PID controller
---

Sebastian Thrun' s [TED Talk][1] and [TEDx Brussels Talk][2] struck me. Shortly
after, I found his [Artificial Intelligence for Robotics Course][3] at Udacity
and thought I could start a hobby project, a self-driving RC car. If this hobby
project makes fair amount of progress it could hopefully pave the way for
miniature version of [DARPA Grand Challenge][4] with RC cars. Moreover, we can
move [solar cars][5] one step further and arrange driverless solar car races.
Article [Stanley: The Robot that Won the DARPA Grand Challenge][6] gives great
insight into the format of the challenge. Miniature challenge can also use a
similar format.

<iframe src="https://embed-ssl.ted.com/talks/sebastian_thrun_google_s_driverless_car.html" width="640" height="360" frameborder="0" scrolling="no" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

Having taken the course at Udacity, I have just started implementing a
self-driving RC car. The car is fundamentally going to be made up for four broad
subjects. Path tracking control, localization, obstacle avoidance, and path
planning. I hope to cover multiple approaches for each subject as I learn them.
I use [Jupyter Notebook][7] to study and document theoretical concepts and
implement them on Arduino to see how they look like in practice.

Here is the video for the first part in the series. Please see the [notebook][8]
for the full explanation of technical details. [Arduino source code][9] is also
available.

<iframe width="560" height="315" src="https://www.youtube.com/embed/rEmxMAjLMeY" frameborder="0" allowfullscreen></iframe>

[1]: https://www.youtube.com/watch?v=bp9KBrH8H04
[2]: https://www.youtube.com/watch?v=r_T-X4N7hVQ
[3]: https://www.udacity.com/course/artificial-intelligence-for-robotics--cs373
[4]: https://www.youtube.com/watch?v=M2AcMnfzpNg
[5]: http://teamsolaris.com/site/eng/index.html
[6]: http://robots.stanford.edu/papers/thrun.stanley05.pdf
[7]: http://jupyter.org/
[8]: https://github.com/gokhanettin/driverless-car-notes/tree/v0.1.0
[9]: https://github.com/gokhanettin/driverless-car/tree/v0.1.0
