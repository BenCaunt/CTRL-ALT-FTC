---
description: >-
  Homeostasis provides a few different types of controllers, all of which with
  their own utility.
---

# Included Controllers

## PID Control&#x20;

### BasicPID&#x20;

```java
// creation of the PID object
PIDCoefficients coefficients = new PIDCoefficients(Kp,Ki,Kd);
BasicPID controller = new BasicPID(coefficients);
// usage of the PID 
while (true) {
    double output = controller.calculate(targetPosition, measuredPosition);
}
```

BasicPID.java is a primitive PID implementation that is good to get something working quickly but may not be optimal for all situations. Many issues such as derivative noise and integral windup may cause issues with the controller. As a result we have created "PIDEx"

### PIDEx&#x20;

```java
// creation of the PID object

PIDCoefficientsEx coefficients = new PIDCoefficientsEx(Kp,Ki,Kd,integralSumMax
                                                         stability_thresh,
                                                          lowPassGain);
// usage of the PID 
PIDEx controller = new PIDEx(coefficients);
while (true) {
    double output = controller.calculate(targetPosition, measuredPosition);
}
```

This PID controller implements a few crucial features on top of the traditional PID Controller. &#x20;

1. Integral reset on zero crossover: As the error crosses over zero, we reset the integral to prevent moving in the wrong direction.&#x20;
2. We have a stability threshold argument, basically if the derivative (effectively speed) is above this threshold we deem there is no reason to run the integral calculation.  This helps remove overshoot.&#x20;
3. We cap the integral sum of our PID controller to ensure that we don't saturate our system.  This helps with integral windup.  For traditional FTC motors where the power is -1 < x < 1, integralSumMax \* Ki should be less than or equal to 1.&#x20;
4. We use a low pass filter on the derivative.  This helps remove noise from the measurements.&#x20;

## Full State Feedback Control

Full State feedback is an approach where we perform simultaneous feedback on each state (position, velocity, etc) of our system in parallel. To make this easy we use a custom vector class that is effectively an extension of an array that provides helpful methods such as the dot product.&#x20;

### FullStateFeedback

```java
// This example is a two state, position velocity system. 
// Similar to the PID coefficients 
Vector K = new Vector(new double[] {1.1,0.3});
FullStateFeedback controller = new FullStateFeedback(K);

while (true) {
    // measured position and velocity 
    Vector state = new Vector(new double[]{position, velocity}); 
    // target position and velocity
    // protip - these can be generated with motion profiles!
    Vector reference = new Vector(new double [] {referencePosition, referenceVelocity}); 
    double command = controller.calculate(reference,state); 
}
```

This type of controller works especially well with motion profiles.  Homeostasis uses a trapezoidal motion profile borrowed from WPILib. We also recommend, if available to use the roadrunner motion profiles. This controller is able to follow references along multiple states at once, allowing it to perform better in these type of trajectories than PID.&#x20;

## Bang Bang Control

While not recommended for the majority of tasks, Bang Bang control can often be the right tool for the job if one needs a very agressive controller and doesn't mind either oscillations or large steady state error. java

```java
double maxOutput = 1;
double tolerance = 3; 
BangBangParameters parameters = new BangBangParameters(maxOutput,tolerance);
BangBang controller = new BangBang(parameters); 
while (true) {
	double command = controller.calculate(referencePosition, position); 
}
```

This controller takes in two parameters, it's output power and a tolerance.  This controller works by making a simple comparison: is it too low or is it too high and then applies either maxOutput or -maxOutput to the system.  If the error is within tolerance (or hysterisis as the parameter is called), then the controller will return 0. This hysterisis prevents the bang bang controller from oscillating forever but has the trade off of introducing some state error.&#x20;

## Dealing with Angles

The previously mentioned linear feedback controllers will fail in certain circumstances such as using them in combination with an IMU to turn the robot.  For this case it is recommended to use the AngleController wrapper to correct this.  This wrapper implements the techniques discussed in the "[Dealing with Angles](https://www.ctrlaltftc.com/controlling-heading)" section of CTRL ALT FTC. &#x20;

Here we can see how to use this wrapper:

```java
double targetAngle = Math.toRadians(90);
PIDCoefficients coefficients = new PIDCoefficients(0.5,0,0);
// any controller that implements "FeedbackController" can be used.
BasicPID pid = new BasicPID(coefficients);  
AngleController controller = new AngleController(pid); 

while (true) {
	double command = controller.calculate(targetAngle, readIMURadians()); 
}
```

An important note is that AngleController can be used with nearly controller in homeostasis and even your own! Just make sure that this controller implements the "FeedbackController" interface!&#x20;

## Feedforward Control

As we have learned from the rest of the website, Feedforward control is an open loop controller, meaning it only takes in the target state and does not require a measurement.  Generally this works very well with velocity control in FTC due to the tendency for velocity measured by encoders to be quite noisy; making closed loop control difficult.&#x20;

### BasicFeedforward

This implements a basic, Velocity, Acceleration, Static controller

```java
double Kv = 1.1;
double Ka = 0.2;
double Ks = 0.001; 
FeedforwardCoefficients coefficients = new FeedforwardCoefficients(Kv,Ka,Ks);
BasicFeedforward controller = new BasicFeedforward(coefficients); 
while (true) {
	double command = controller.calculate(0,referenceV,referenceA);
}
```

This controller takes in three arguments, a target position, a target velocity, and a target acceleration.  Please note, **the target position will not actually do anything in BasicFeedforward**. This controller will then use Kv and Ka to approximate the desired motor output.  Ks stands for static and is the minimum power in either direcion that our motor can output. This is to be tuned in a way that allows our system to respond much more quickly at lower reference velocities.&#x20;

### FeedforwardEx

This controller takes BasicFeedforward and adds better support for arm and elevator systems.&#x20;

```java
double Kv = 1.1;
double Ka = 0.2;
double Ks = 0.001;
// Kg and Kcos should NOT be used together, one or the other should be zero. 
double Kg = 0.1;
double Kcos = 0.1;
FeedforwardCoefficientsEx coefficientsEx = new FeedforwardCoefficientsEx(Kv,Ka,Ks,
								         Kg,Kcos);
FeedforwardEx controller = new FeedforwardEx(coefficientsEx);
while (true) {
	double command = controller.calculate(x,v,a);
}
```

This is a more advanced feedforward controller that extends the basic feedforward controller.  It adds angle compensation for arms and gravity compensation for elevators / linear slides.  The intent is that Kg and Kcos should be used exclusively and not with one another.  kg provides a constant upward torque to counteract gravity and Kcos is multiplied by the reference angle of your to compensate for the nonlinear effects of it rotating.  **IF Kcos is USED X MUST BE IN RADIANS.**  With Kcos and Kg, these should be tuned so the system can hold its weight without feedback.&#x20;

## No Control

When using a system where we don't want a particular type of control (we only want a feedforward or a feedback controller, not both) there exists NoFeedback and NoFeedforward which simply act as dummy classes.
