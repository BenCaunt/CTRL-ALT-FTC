---
description: >-
  Roadrunner is a library used by many successful teams, and understanding why
  we utilize it in specific ways and not others is key to avoiding common
  pitfalls.
---

# Roadrunner Control Explained

The Roadrunner library and Quickstart project accomplish three main things: \
1\. Localization - using sensors such as drive encoders or [dead wheels](https://gm0.org/en/latest/docs/common-mechanisms/dead-wheels.html) to estimate your robot's position on the field.\
2\. Trajectory Generation - Roadrunner lets you create paths your robot follows. These paths are unique because they also calculate your robot's exact position, velocity, and acceleration at each point in time. Trajectories make your autonomous routines more consistent. \
3\. Control - Using algorithms like [PID](../the-pid-controller/) and [feedforward](../feedforward-control.md), which have previously been covered on CTRL ALT FTC, we can achieve high performance in following roadrunner's trajectories.

### Why velocity PID is not recommended

On the FTC discord and other community forums, it is often recommended to use feedforward instead of velocity PID, even when using drive encoders!

{% hint style="success" %}
Individuals new to the FTC may believe the recommendation to set RUN\_ _USING \_ ENCODERS (RUE) to FALSE_ will disable the use of encoders entirely. Instead, all it does is disable the use of velocity PID for the .setPower method of the motors. With RUE enabled, the motors will use PID to attempt to maintain a velocity as a percentage of the maximum.
{% endhint %}

1. Feedforward has less phase lag than PID Control.\
   \
   For PID control to produce a motor command, there must be an error between the current and desired states. This means that, by definition, your system is not perfectly following the velocity/acceleration trajectory. Feedforward models your robot's dynamics and produces nearly perfect (ish) motor powers to reach the desired trajectory state.
2.  Feedforward is more stable than PID Control.\
   Since velocity control is asymptotically stable, meaning that if you give your motor an arbitrary power, it will converge to a constant velocity, there isn't a need to have the precise feedback of a PID loop. Sensor noise from how velocity is calculated from the encoders can also result in further degraded performance.

#### But what about robustness?

A concern arising from the suggestion not to use feedforward is that your robot will not be able to reject disturbances as a PID Controller would. What is missing from this, though, is that you still will use PID! Since you also need to tune a position control loop, you aren't losing ANY robustness to disturbances. You are only making it easier to respond to these changes by using feedforward.

<figure><img src="../.gitbook/assets/Roadrunner Control System.png" alt=""><figcaption><p>Visual Overview of how the roadrunner control system is constructed</p></figcaption></figure>

As we can see from the graphic above, regardless of whether we use feedforward or PID, we will still get corrections from the position PID, ensuring robustness.
