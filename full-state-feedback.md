---
description: A modern control theory approach to feedback control
---

# Full State feedback

If you recall from our introduction to closed loop control and the proportional controller from earlier we can roughly control a systems state using the formula:

$$
u(t)=k(r(t)-x(t))
$$

Where u\(t\) is our current motor input  
              x\(t\) is our current system state  
               r\(t\) is our current reference

Previously we used this in combination with an integral and derivative term to improve performance but this may not be optimal for all scenerios, for example nonlinearities such as those introduced in the "Improvements to the PID Controller" section can hinder trajectory tracking performance.

Full state feedback for a single state takes a different approach

$$
u(t)=  k_1(r_1(t)-x_1(t))...+k_n(r_n(t)-x_n(t))
$$

Full state feedback control allows us to control each relevant state of our system. The formula above shows that we are beginning with our primary state x\_1 and couple the outputs with each other state of our system.  An example of what these states could be is position, velocity, etc.

```java
/**
* Calculate state feedback for position and velocity of our system. 
*/
public double calculateStateFeedback(double targetPosition, double targetVelocity) {

    double positionError = targetPosition - robotPosition;
    double velocityError = targetVelocity - robotVelocity; 
    double u = (positionError * k1) + (velocityError * k2); 
    return u; 
    
} 
```

This type of controller will have much better trajectory tracking performance than pid as in addition to driving our robot to the target position, it is able to drive our robot at target velocity.  In combination with motion profiling this is an incredibly powerful tool.  You can even extend this method by adding additional states.  Combined with feedforward control you will have an incredibly robust controller. 



