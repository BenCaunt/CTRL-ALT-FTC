---
description: Re-opening the loop
---

# Feedforward Control

In the previous chapters, we focused on methods of feedback control. Feedback control is excellent for many systems and allows for effects such as disturbance rejection to improve the system's performance. While this approach is exemplary, we can often increase performance even further with the addition of an open-loop feedforward controller.

‌Feedforward works by essentially 'predicting' the mapping between plant inputs and outputs rather than iteratively converging on the desired result with a feedback controller.

‌In general, the advantage of feedforward (or open-loop) control is that it is immune to both time delay and sensor noise. This means that, unlike feedback control, bad sensor data or delays between inputs are much less likely to cause a system to be unstable.

Another great use for feedforward is to replace the integral term of a feedback controller. Using feedback as an alternative to specifically the integral term is often a valid solution if one is worried about issues such as integral windup impacting the system's performance.

### Software implementation of feedforward

```java
double output = K * reference;
```

As you can see, feedforward is more trivial compared to the feedback control loops such as PID that we implemented in previous chapters. This is because feedforward is acting almost as a unit conversion between some input and some output. This conversion is determined by the tuned value `K` in the code snippet above.

In FTC this value is often referred to as `Kv`, specifically when dealing with controlling the velocity of a plant. Additionally, a term for acceleration can also be added, commonly referred to as `Ka`.

```java
double output = (Kv * referenceVelocity) + (Ka * referenceAcceleration);
```

Using acceleration and velocity references together for our output allows our system to follow trajectories. Trajectories effectively tell our system at any given point in time what the system's states should be.

### Closing the loop

We would often like to have the benefits such as improved disturbance rejection that feedback control offers while keeping the stability that feedforward control provides. We can achieve this by coupling the outputs together.

Here we can see a proportional and feedforward controller for a velocity system:

```java
while (setPointIsNotReached) {

    double output = Kp * (referenceVelocity - currentVelocity) + Kv * (referenceVelocity);

}
```

We can also use feedforward in combination with a full PID controller such as the one we developed in previous chapters. Often, this setup can be referred to as PIDF:

```java
while (setPointIsNotReached) {

    double output = PID(referenceVelocity, currentVelocity) + Kv * (referenceVelocity) + Ka * referenceAcceleration;

}
```

### Arm Feedforward

Rotating arms such as the one in the image below can be very easy to make, but can be very challenging to program. This is because of the nonlinear dynamics caused by gravity as the arm rotates. In order to compensate for these nonlinear dynamics, we can use a gravity feedforward to linearize the system.

![FTC 8300 Ultimate Goal Robot with Rotating Arm](<.gitbook/assets/Screen Shot 2021-12-02 at 8.57.29 PM.png>)

To compensate for the force of gravity, we take the cosine of our target angle relative to the ground, as shown in the following example.

```java
double Kcos = SOME_VALUE_YOU_TUNE;
double reference = YOUR_TARGET_ANGLE;
double power = Math.cos(reference) * Kcos;
```

We can then use the code above with a PID controller or another type of feedback controller. Using this will improve the reliability of the control system, as it will more accurately compensate gravity than a simple integrator. However, we recommend using both this and an integrator for optimal results.

This works because `cos()` represents the ratio between the adjacent side of a right triangle and the hypotenuse of the triangle. Whenever the arm is extended straight out (0 degrees), the value of the cosine function is at its maximum (1). This is the point where the system demands the most torque to hold its weight. Whenever we want the arm to be oriented straight up or down (90 degrees), the arm does not require any torque to hold its weight. This matches our feedforward controller, as `cos(90 degrees)` is 0.

By using a nonlinear feedforward controller, we can improve the reliability of our control system and improve the performance of our system.

### Slide Gravity Feedforward

For cascading linear slides, the force of gravity is generally nearly constant. For continuously rigged linear slides, this assumption is not as true, but the difference is generally negligble, so it can be ignored.

In physics, the force of gravity is equal to the mass of a system \* the gravitational constant `g`.

$$
f=mg
$$

Combined with Newton's Second Law:

$$
f=ma
$$

We can conclude that the acceleration due to gravity is constant:

$$
ma=mg \\ a = g
$$

In order to compensate for this, we can simply tune a constant power to reject the disturbance:

```java
double Kg = // tune till the slide holds itself in place.

/*
 * PID for general movement of the system
 * The feedforward removes the disturbance
 * This improves the responsiveness of the PID controller
 */
double output = PID(reference, state) + Kg;

```
