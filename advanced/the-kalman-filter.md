---
description: >-
  State observation is a crucial component of control theory but is often
  overlooked in FTC
---

# The Kalman Filter

## Why Kalman Filter?

A Kalman filter at the highest level is an algorithm that optimally estimates any given state of a system, given a model of how the system changes over time and knowing a set of sensor measurements. We use a Kalman filter whenever we have doubts about the quality of our sensors, and we require more reliable measurements to control our system with the performance that we desire.

Imagine trying to move your robot from one point to another. There are two general ways you can approach this. One way would be to model a path that should theoretically take your robot to the point (for example, a motion profile). The other would be to move your robot until a sensor tells you you’ve gotten to the point. Both of these approaches have drawbacks: a modeled path will never be perfect as there are always factors you can’t account for, and a sensor may have drift and/or unreliable measurements. The magic of the Kalman filter is that it combines these methods, allowing for a lessening of the impacts of these drawbacks.

The Kalman filter uses the robot’s current state (ex: position, velocity) to predict where it will be in the future. This prediction can vary in complexity, from comprehensive models with many variables to much simpler models. Then, when a new sensor measurement comes in, it updates this prediction. By blending the predictions and measurements you obtain a more accurate estimate than you could get from just the predictions or the measurements alone.

![LR GYRO + Open Odo + Kalman Filter for Angle Estimation](../.gitbook/assets/optimal-sensor-estimation.png)

## How does the Kalman Filter work?

A quick disclaimer: this page is intended to build enough intuition about the Kalman filter to allow you to implement a Kalman filter for FTC applications yourself. As such, many simplications have been taken and this explanation is by no means mathematically rigorous.

Now, let’s start with how the Kalman Filter keeps track of the variables we want it to estimate. For a given variable, such as a robot’s angle, we have a value that is associated with that variable (ex: $$15$$ degrees). However, in addition we also want to know how much uncertainty exists in that variable. The way the Kalman filter assigns uncertainty to a variable is through the metric of variance, with a high variance corresponding to high uncertainty.

With that, we can begin to understand how the Kalman filter works. We will begin with the prediction step:

$$x_t=x_{t-1}+u_t$$

In this equation, $$x$$ represents the value of the variable we want to track and u represents how much we believe the value will change. You will notice that $$x$$ has subscript which denotes the time step, with t-1 meaning the previous time step and t being the current time step. Putting this all together, this equation shows that our predicted value is the sum of our previously estimated value and the amount we believe the value will change. The value of $$u_t$$ is something you will need to determine yourself how to calculate (ex. Change in position from odometry for velocity).

When we make a prediction, we are also changing the uncertainty we have in that variable. This takes form in the following equation:

$$p_t = p_{t-1} + q$$

Here, $$p$$ represents the variance of $$x$$ and $$q$$ represents the variance of our model. When we use our model, we always add uncertainty to our estimate because we can’t be certain our model is correct. The amount of uncertainty in our model, $$q$$, is something that you will have to tune to your system.

Now that we have a prediction for what our variable will be, we will combine it with our sensor measurement. We update our value using this equation:

$$x_t = x_{t-1}+K_t(z_t-x_t)$$

In this equation, $$z_t$$ is the value of our measurement and Kt is a constant between $$0$$ and $$1$$ that we will later define. The expression $$(z_t-x_t)$$ is the difference from the new measurement we just made and what we have already estimated the state to be. That is being multiplied by $$K_t$$, which is also called the Kalman gain. This number dictates the influence of the measurement in the estimate. We will see how this number is calculated next, but notice that if $$K_t=1$$, then $$x_t=z_t$$ and if $$K_t=0$$, $$x_t=x_t$$. The closer $$K_t$$ is to $$1$$, the more trust we put in the measurement, and the closer it is to $$0$$, the more trust we put in our model.

Let’s now compute the Kalman gain as follows:

$$K_t = \frac{p_t}{p_t+r}$$

$$r$$ is the variance in our measurement. You may notice that this formula is a simple proportion, and that makes a lot of sense. If $$p_t$$ is high compared to $$r$$, $$K_t$$ will have a value close to $$1$$. This makes sense because if there is more uncertainty in our model than our measurement we should weigh the measurement more, which a $$K_t$$ value close to $$1$$ achieves. In the opposite case, when $$r$$ is high compared to $$p_t$$ then $$K_t$$ has a value close to $$0$$, favoring the model more than the measurement.

Alas, when we update our estimated value we must also update its uncertainty.&#x20;

$$p_t=(1-K_t)p_t$$

Since $$K_t$$ ranges from $$0$$ to $$1$$, $$(1-K_t)$$ will also range from $$0$$ and $$1$$, so our measurement will always decrease uncertainty in our estimate. When $$K_t$$ is close to $$0$$, we do not factor in the measurement much and therefore the uncertainty remains largely unchanged. When $$K_t$$ is close to $$1$$, we are very confident in our measurement and therefore will have low uncertainty.

## Putting it all together

Finally, we have each of the equations we need for our single input-single output Kalman filter. Now we can put them together and then we will be able to effectively implement our filter in software. Taking each simplified part and putting them into a computable procedure will yield:

**Set Initial Conditions**

$$x=\text{initial system state}$$&#x20;

$$q=\text{model variance}$$&#x20;

$$r=\text{sensor variance}$$&#x20;

$$P=\text{initial guess}$$&#x20;

$$K=\text{initial guess}$$

**loop:**

1. $$x_t=x_{t-1}+u_t$$                         project our state estimate
2. $$p_t = p_{t-1} + q$$                           project our variance
3. $$K_t = \frac{p_t}{p_t+r}$$                                 calculate the Kalman gain
4. $$x_t = x_{t-1}+K_t(z_t-x_t)$$       update estimation based on sensor reference
5. $$p_t=(1-K_t)p_t$$                       update error variance

## Kalman Filter Pseudocode

```java
double x = 0; // your initial state
double Q = 0.1; // your model covariance
double R = 0.4; // your sensor covariance
double p = 1; // your initial covariance guess
double K = 1; // your initial Kalman gain guess

double x_previous = x;
double p_previous = p;
double u = 0;
double z = 0;

while (true) {

    u = getInput(); // Ex: change in position from odometry.
    x = x_previous + u;

    p = p_previous + Q;

    K = p/(p + R);

    z = getSecondSensor(); // Pose Estimate from April Tag / Distance Sensor

    x = x + K * (z - x);

    p = (1 - K) * p;

    x_previous = x;
    p_previous = p;

}
```

Finally, you have now implemented one of the most important filters in modern control theory.

### Implementation Example

Here you can find a Jupyter notebook for a Kalman filter that fuses a motion profile and velocity sensor: [https://github.com/BenCaunt/Kalman-Filter-Experiments/blob/main/velocity%20kalman%20filter%20example%20.ipynb](https://github.com/BenCaunt/Kalman-Filter-Experiments/blob/main/velocity%20kalman%20filter%20example%20.ipynb)

## Multiple Sensors

You can also use multiple sensors with the Kalman Filter! For each loop, you can first run a prediction and then for each additional sensor can have an update loop. In some cases, however, you'd be better off using a weighted average of all the sensor readings.

## Limitations of our work

For simplicity's sake, we made the assumption that the majority of systems we will be dealing with in FTC are single-input, single-output systems. Unfortunately, this is not guaranteed and you may have to end up with a more complicated filter where you must use matrices instead of scalar values. For that, you will need to use a library such as [EJML ](http://ejml.org/wiki/index.php?title=Main\_Page)and remove lots of the simplifications that we made.
