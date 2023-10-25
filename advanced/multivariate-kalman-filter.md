# Multivariate Kalman Filter

### Recap
In the previous page, we learnt how to use a kalman filter to estimate the state of a system given measurements. However, we generalized the findings to SISO (Single Input Single Output) systems. And that's still a lot! We can use the Kalman Filter to get reliable measurements of scalars like an IMU angle or a robot's distance to the wall. But what if we wanted to deal with MIMO Systems? This page will give an example of a scenario like this. 

### What's MIMO?
MIMO stands for Multi Input Multi Output. This describes processes that take in more than one input and return more than one output. For example, your drivetrain is a MIMO system! Your driver inputs forward, rotational (and strafe if holonomic) powers. The outputs are linear displacement in the xy directions, and angular displacement. 

### Tracking Position and Velocity
Let's say we have a robot moving in one dimension (for simplicity). The robot will start at a wall, and has a sensor that returns how far the wall is to the robot. Furthermore, we have a single odometry pod to get the position of the robot. However, we want to track both the position and velocity of the robot. We need start using matricies!

## Prediction Step

### Describing our System State
We want our system state to contain both the position and the velocity of the robot. To do this, we can define it as a 2 by 1 matrix as such:
$$\pmb{x} = \begin{bmatrix} x  \\ \dot{x} \end{bmatrix}$$
(From now on, we will use a bold x to represent the system matrix) 

### Initial State Covariance
Now we need to create our covariance matrix $P$. This is basically an initial guess of how "certain" we are about the position and velocity. Unlike the State Matrix, this matrix is going to be a 2 by 2 matrix. 
$$\pmb{P} = \begin{bmatrix}  a & 0 \\ 0 & b \end{bmatrix} $$
$a$ is how certain we are about the position, $b$ for velocity. The less certain you are about a variable, the higher those values should be. The 0's in the matrix are actually covariance values (essentially how position and velocity are related), but it is best to leave it at 0 and have the filter update it. Don't spend a lot of time working with this, this is just an initial guess!

### Design Process Model
Now, we need to design a matrix that helps predict the next state after a time step. 
In other words, we want to find 2x2 matrix $\pmb{F}$ such that 
$$\pmb{x}_{t} = F\pmb{x}_{t-1}$$

From physics, we know that $$x_{t} = \dot{x} * \Delta{t} + x_{t-1}$$ 
$$\dot{x}_{t} = \dot{x}_{t-1}$$

since we are not assuming acceleration between timesteps (if we were, we could design our state to include $\ddot{x}$). The following is the same thing described in matricies (try confirming this by expanding it yourself):
$$\pmb{x}_{t} = \begin{bmatrix}  1 & \Delta{t} \\ 0 & 1 \end{bmatrix}\pmb{x}_{t-1}$$
Great! We found our state transition matrix! 

### Design Process Noise
This is basically our $\pmb{Q}$ variable. It is used to describe outside factors that can make our model inaccurate. For example, the small bumps in the mat the robot is on and wheel slip. It is not easy to find actual values of $\pmb{Q}$, however the larger the numbers are in this 2x2 matrix, the more the environment affects the model. These values are best to be tuned using trial and error.

### Adding Control Input (optional)
Unless you want the filter to assume you are going at a constant velocity, we probably should update the state equation based on control inputs! Luckily, this is not that hard as we already know how to use State Space:
$$\pmb{x}_{t} = F\pmb{x}_{t-1} + \pmb{B}u$$
We could have $u$ be like a motor power, and the 2x1 matrix $\pmb{B}$ map the motor power to the velocity of the robot. In other words:
$$\pmb{B} = \begin{bmatrix}  0 \\ kP \end{bmatrix} $$

### Putting it All Together
First, we predict the system state at time $t$. This is done by using the model:
$$\pmb{x}_t = \pmb{F}\pmb{x}_{t-1} + \pmb{B}u$$
Then,we predict the covariance matrix using this formula:
$$\pmb{P} = \pmb{F}\pmb{P}\pmb{F}^T + \pmb{Q}$$
The fancy T exponent means transpose. <br>

Great! We are done with the prediction step, so lets move on.
## Update Step

### System Uncertainity
The equation here is a bit tedious, but the goal is to be able to "use" our covariance matrix $\pmb{P}$ with our measurement noise $\pmb{R}$. It's just like how you need to convert your robot-relative coordinates to global field coordinates for a pose estimate. The resulting matrix basically tells us the covariance of our innovation. 

$$\pmb{S} = \pmb{H}\pmb{P}\pmb{H}^T + \pmb{R}$$

### Kalman Gain
This is the crux of the Kalman Filter. What we're trying to do is to pick an estimate between our prediction and measurement. To optimally do this, the filter uses covariances of our prediction and measurement. The Kalman Gain is basically a ratio that tells us where to pick the value. The higher it is, the closer the estimate will be to the measurement. This is the general idea, but it becomes unclear when we're dealing with matricies! Either way, the equation is 
$$\pmb{K} = \pmb{P}\pmb{H}^{T}\pmb{S}^{-1}$$
Do note that $\pmb{S}^{-1}$ means we're taking the inverse of the system uncertainity matrix.

### Measurement Noise
This matrix is an estimate of the noise of our sensor(s). If you have ever used some sort of distance sensor on a robot, you know that there is some variation in readings over time. Since we only have one sensor measuring a single thing, the noise matrix $\pmb{R}$ can just be a single number describing how "certain" we are about this measurement. Once again, finding this value comes from trial and error.
$$\pmb{R} = \begin{bmatrix} c \end{bmatrix} $$
### Finding the Innovation
The innovation is basically describing the error between our prediction and the sensor reading. When we were dealing with univariate systems, this was pretty simple. However, we now must note that our sensor only returns the position, and our prediction has both position and velocity. In other words, we can't just subtract. We need to multiply our prediction by a matrix, so we can find the innovation:

$$\pmb{y} = \pmb{z} - \pmb{H}\pmb{x}$$
Note that $\pmb{z}$ is 1x1 (because the sensor only measures position), so $\pmb{H}$ must be 1x2.  To find the innovation, we need to void the velocity because the sensor doesn't measure it. Thus, we set $\pmb{H}$ as 
$$ \pmb{H} = \begin{bmatrix} 1 & 0 \end{bmatrix}$$


### Putting it All Together
First, we get the system uncertainity $\pmb{S}$:
$$\pmb{S} = \pmb{H}\pmb{P}\pmb{H}^T + \pmb{R}$$
Then, we find the Kalman Gain:
$$\pmb{K} = \pmb{P}\pmb{H}^{T}\pmb{S}^{-1}$$
Now, we just found our innovation (fancy word for residual):
$$\pmb{y} = \pmb{z} - \pmb{H}\pmb{x}$$

All that's left to do is find our final estimate of $\pmb{x}$ and update the covariance matrix $\pmb{P}$! We're very close, and luckily the final two equations are not as daunting:
$$x_t^+ = x_t^- + \pmb{K}y$$
$$\pmb{P}^+ = (\pmb{I} - \pmb{K}\pmb{H})\pmb{P}^-$$
The positive and negative symbols basically means after update and before update basically. Furthermore, $\pmb{I}$ is the identity matrix. It will be a 2x2 matrix with values on the diagonal equal to $1$ and values on the off-diagonal equal to $0$:
$$\pmb{I} = \begin{bmatrix}1 & 0 \\ 0 & 1 \end{bmatrix}$$

Whew! That was a lot. But now we're able to get optimal estimate for our robot's position and velocity. 

### Summary
This was an example of using a Kalman Filter in a MIMO system. If we wanted to include another parameter, i.e. acceleration, we could change the matrix dimensions and update our noise matrices to reflect the changes. For example, our $\pmb{P}$ matrix would be
$$\pmb{P} = \begin{bmatrix}a & 0 & 0 \\ 0 & b & 0 \\ 0 & 0 & c \end{bmatrix}$$
There are also variations of the Kalman Filter that can be used based on different scenarios. If you are interested in learning more, the bottom section has some good resources on where to learn more! 

## Further Reading

https://github.com/rlabbe/Kalman-and-Bayesian-Filters-in-Python
<br>
The above is a very good online book that gives a beginner level approach towards the Kalman Filter (including Multivariate applications), and variations of it like the Extended Kalman Filter and the Unscented Kalman Filter. 
