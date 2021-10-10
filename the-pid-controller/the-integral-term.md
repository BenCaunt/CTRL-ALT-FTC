---
description: Introduction to the integral term
---

# The Integral Term

## The Integral Term

The Integral term of a PID controller can be thought of as an extra bit of "kick" that the controller needs to converge on its reference . Integration is the process of adding up all of the parts into a whole. In this case, integration is the sum of all of the errors over time.

{% hint style="info" %}
Integration is usually denoted by the symbol usually denotes integration '∫ '. This symbol is generally used to describe a function that gives the area under the line of another part. This may seem a little bit daunting to those without calculus knowledge, but trust me, it's straightforward in this context. 
{% endhint %}

Traditionally in calculus, we are dealing with _**continuous**_ functions such as f(x) = x^2. In FTC, we do not have this privilege. We are instead required to use _**discrete**_ measurements. This means that we are getting new data at some intervals without having sizes in between. This, unfortunately, means that our integration will not be exact, and some approximations will be made.  

## How do we Integrate discrete data?

Integration of discrete data is inherently very different than that of continuous systems. This is not necessarily a bad thing for us. While it means that we have to make some approximations that will lead to slight inaccuracies, this doesn't matter too much for PID. The most common method of integration of discrete systems is known as "forward Euler integration".

\
Forward Euler Integration works by the following sequence:

* Measure the data at the current time step.
* Calculate the time since we last recorded data.
* Multiply the data by the time since we last recorded data.
* Add the product to a running total containing the integral sum.
* repeat until we are out of data (or keep repeating forever, whatever is necessary).

![Forward Euler Integration ](<../.gitbook/assets/Forward euler integration example .png>)

{% hint style="success" %}
For you calculus inclinded individuals, Forward Euler Integration may already be familiar to you as riemann sum.
{% endhint %}

 As you can see from the figure above, Forward Euler Integration can approximate the integral of the data by taking measurements at discrete points and then solving for the area as a rectangle. While this does tend to undershoot or overshoot the authentic integral, the differences are pretty negligible for this application.

### Software implementation

We actually imeplemented forward Euler integration already in the Software implementation of PID in our introduction to PID.

```
integralSum = integralSum + (error * timer.seconds());
```

This integral sum is then added to the output of our controller which is then sent as the input to our system.  

## Effect of Integration in PID 

Integration is useful because it forces our error to be pushed toward 0 over time.  This allows our system to be much more robust against disturbances such as gravity, friction, and backlash.

![Our PID controller with Kp = 2, Ki = 0.3](<../.gitbook/assets/Kp = 2, Ki = 0.3 model.png>)

![System response with Kp = 2, Kd = 0, Ki = 0.3](<../.gitbook/assets/Kp = 2, Ki = 0.3 result.png>)

As we can see from the graph above, the integrator works to squash our error down to zero over time. This result, however, is still not quite perfect as we still have a large oscillation around our reference. The next chapter will resolve this issue.\
\
