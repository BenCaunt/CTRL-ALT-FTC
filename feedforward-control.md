---
description: Re-opening the loop
---

# Feedforward Control

In the previous chapters, we focused on methods of feedback control. Feedback control is excellent for many systems and allows for effects such as disturbance rejection to improve the system's performance. While this approach is exemplary, often, we can increase performance even further with the addition of an open-loop feedforward controller.

‌ Feedforward works by essentially 'predicting' the mapping between plant inputs and outputs rather than iteratively converging on the desired result with a feedback controller.

‌ The advantage of feedforward / open-loop control, in general, is that it is immune to both time delay and sensor noise. This means that, unlike feedback control, bad sensor data or delays between inputs are much less likely to cause a system to be unstable.

Another great use for feedforward is to replace the integral term of a feedback controller.  Using feedback as an alternative to specifically the integral term is often a valid solution if one is worried about issues such as integral windup impacting the systems performance.  

### Software implementation of feedforward

```java
double output = K * reference;
```

As you can see, feedforward is farily trivial compared to the feedback control loops such as PID that we implemented in previous chapters.  This is because feedforward is acting almost as a unit conversion between some input and some output.  This conversion is determined by the tuned value K in the code snippet above.

In FTC this value is often referred to as 'Kv', specifically when dealing with controlling the velocity of a plant.  Additionally a term for acceleration, fittingly called 'Ka' can also be added. 

```java
double output = (Kv * velocityReference) + (Ka * accelerationReference);
```

 Using acceleration and velocity references in combination for our output allows our system to follow trajectories. Trajectories effectively tell our system at any given point in time what the system's states should be.

### Closing the loop

We would often like the benefits such as improved disturbance rejection that feedback control offers while keeping the stability that feedforward control provides. We can achieve this by coupling the outputs together.

Here we can see a proportional + feedforward controller for a velocity system: 

```java
while (setPointIsNotReached) {

    double output = Kp * (referenceSpeed - currentSpeed) + Kv * (referenceSpeed);

}
```

We can also use feedforward in combination with a full PID controller such as the one we developed in previous chapters, often this setup can be referred to as PIDF:

```java
while (setPointIsNotReached) {

    double output = PID(referenceSpeed, currentSpeed) + Kv * (referenceSpeed) + Ka * referenceAccel;

}
```

