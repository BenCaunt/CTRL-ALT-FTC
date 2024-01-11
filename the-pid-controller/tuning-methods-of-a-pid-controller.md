---
description: >-
  Now that we understand the fundementals of a PID controller, how do we tune
  it?
---

# Tuning Methods of a PID Controller

## Tuning Methods

#### Manual Tuning

There are a few different ways to approach manually tuning a PID controller, but a popular one appears to be the following:

1. Start with Kp, Ki, and Kd at 0.
2. Increase Kp until steady-state error is very low.
3. Increase Ki until steady-state error is removed entirely.
4. Increase Kd until oscillations are removed.

This method above works well for many systems but many people have better luck with other methods.

Manning PID controllers manually require a little bit of experience, but this knowledge is rudimentary to learn.\
\
 This is a table from the Wikipedia article on PID controllers that characterize how an increase in each term affects the system to get you started.

### Finding reasonable starting values for Kp

Finding a good value for Kp to begin with can be very helpful. To do this you must know the maximum output of your system and the units that you are using to measure your system.

#### Effect of each term on the controller's performance

| Parameter | Rise time     | Overshoot | Settling time  | Steady-state error      | Stability                    |
| --------- | ------------- | --------- | -------------- | ----------------------- | ---------------------------- |
| Kp        | Decrease      | Increase  | Small change   | Decrease                | Degrade                      |
| Ki        | Decrease      | Increase  | Increase       | Eliminate               | Degrade                      |
| Kd        | Little change | Decrease  | Decrease       | Theoretically no change | Improve if Kd is already low |

Table information sourced from: [https://en.wikipedia.org/wiki/PID\_controller](https://en.wikipedia.org/wiki/PID\_controller)

{% hint style="info" %}
Ziegler-Nichols is a method that can yield mixed results, you must be careful not to break your system.
{% endhint %}

#### Ziegler–Nichols Tuning

Ziegler Nichols tuning is a method that is similar to manual tuning in the fact that it requires physical access to the System. Still, it is much more rigorous and, in many situations, can provide superior results. This method requires that you visualize your error over time in some way. There are a few ways to do this. One is to use the [FTC dashboard](https://acmerobotics.github.io/ftc-dashboard/), and another is to use System.out.println() and then copy and paste the data from logcat into excel (suboptimal, I'm aware, but it's a quick fix to get something working).

The Ziegler Nichols procedure works as the following:

1. Start with Kp, Ki, and Kd at 0.
2. Increase Kp until the system is in a steady oscillation around the setpoint.
   1. This is known as the critical gain of the system and is denoted as **Ku**.
3. The oscillation period or the time from peak to peak of the oscillation is a value known as **Tu**.
4. We then use the following lookup table to derive our PID gains.

| Desired Controller | Kp     | Ki        | Kd        |
| ------------------ | ------ | --------- | --------- |
| P Only             | 0.5Ku  | N/A       | N/A       |
| PI Only            | 0.45Ku | 0.54Ku/Tu | N/A       |
| PD Only            | 0.8Ku  | N/A       | 0.1KuTu   |
| PID                | 0.6Ku  | 1.2Ku/Tu  | 0.075KuTu |
| Some overshoot     | 0.33Ku | 0.66Ku/Tu | 0.11KuTu  |
| No overshoot       | 0.2Ku  | 0.40Ku/Tu | 0.066KuTu |

Table sourced from: [https://en.wikipedia.org/wiki/Ziegler%E2%80%93Nichols\_method](https://en.wikipedia.org/wiki/Ziegler%E2%80%93Nichols\_method)

 After following the procedure above, you should theoretically have well-tuned gains that perform as expected depending on the given tuning requirements you chose. However, many do claim that often Ziegler Nichols tuning requires a little bit of fine-tuning to get perfect.

####
