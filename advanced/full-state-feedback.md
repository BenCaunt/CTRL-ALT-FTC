---
description: A modern control theory approach to feedback control
---

# Full State feedback

If you recall from our introduction to closed loop control and the proportional controller from earlier we can roughly control a systems state using the formula:

$$
u(t)=k(r(t)-x(t))
$$

Where u(t) is our current motor input\
&#x20;             x(t) is our current system state\
&#x20;              r(t) is our current reference





Full state feedback takes a slightly different approach than PID control but has the same idea.&#x20;

One can model a linear system in the form:&#x20;

$$
\dot{x}=Ax+Bu
$$

​Where x is the vector that represents the state of your system. A simple model will have two states in this vector; position and velocity.&#x20;

$$
\begin{bmatrix} x  \\ \dot{x} \end{bmatrix}
$$

We want to then move these values to some desired state. such as:

$$
\begin{bmatrix} 0  \\ 0 \end{bmatrix}
$$

We can then perform feedback on this vector directly if we have some gain vector K.

$$
\begin{bmatrix} k_1 \\ k_2 \end{bmatrix}
$$

We want to move x to our desired state of zero. We can perform feedback by first subtracting the observed state from the desired state and then multiplying this vector by the gain K, just as we would for a traditional feedback controller such as PID.

$$
\begin{bmatrix} 0  \\ 0 \end{bmatrix}

-\begin{bmatrix} x  \\ \dot{x} \end{bmatrix}

= - \begin{bmatrix} x  \\ \dot{x} \end{bmatrix}
$$

​we can then compute the dot product between this and K.

$$
\begin{bmatrix} k_1 \\ k_2 \end{bmatrix} \cdot - \begin{bmatrix} x  \\ \dot{x} \end{bmatrix}=-k_1x-k_2\dot{x}
$$

​and so our control input is defined as:&#x20;

$$
u(t)=-k_1x-k_2\dot{x}
$$

​This control law will stabalize x and x-dot to the state of zero but in many systems we want to go to a state other than 0. We can generalize this control law for any desired state by the final control law:&#x20;

$$
\begin{bmatrix}x_r-x\\ \:\:\:\dot{x_r}-\:\dot{x}\end{bmatrix}\begin{bmatrix}k_1\\ \:k_2\end{bmatrix}=k_1(x_r-x)+k_2(\dot{x_r}-\dot{x})
$$

What we have now is effectively just a proportional derivative controller. What makes this different is that at each point in time our target velocity is not necessarily zero. This type of controller will as a result have much better performance following trajectories such as motion profiles and will not rely as much on feedforward control to get good transient performance like a pid controller would (though feedforward is always nice to have!).

What makes this controller even more interesting is when you add extra states. If you were to use a Kalman or some other filter to observe the acceleration of your system you could have even more accurate trajectory following performance. This also goes the other way,  adding a state that is the integral of position will do the same as a PID controller. &#x20;



Full state feedback control allows us to control each relevant state of our system. The formula above shows that we are beginning with our primary state x\_1 and couple the outputs with each other state of our system. An example of what these states could be is position, velocity, etc.

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

This type of controller will have much better trajectory tracking performance than pid as in addition to driving our robot to the target position, it is able to drive our robot at target velocity. In combination with motion profiling this is an incredibly powerful tool. You can even extend this method by adding additional states. Combined with feedforward control you will have an incredibly robust controller.&#x20;

