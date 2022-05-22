---
description: >-
  Let's be real,  the nomen clature around control theory is incredibly
  intimidating at first; it should't be like this. Also this is not in
  alphabetical order lol.
---

# Glossary

1. Reference - The target position we would like to be at.  This is often used interchangably with other terms such as set point or target.
2. Gain - A value we multiply by another value. In a PID controller Kp, Ki, and Kd are "gains"
3. Feedback - a process where given a measurement and a desired state we calculate the best input to reach the state. A synonym for closed-loop control
4. Feedforward - a process where given just a desired state we calculate the best input to reach the state. A synonym for open-loop control
5. PID control - A type of feedback control with three main components.  A proportional, an integral, and a derivative.
6. Integral - the sum of a signal over time&#x20;
7. Derivative - the rate of change of a singal
8. Full State Feedback - a feedback control method that is basically just a bunch of proportional controllers strapped together. &#x20;
9. State - where our system currently is,  usually a combination of position / velocity.&#x20;
10. Roadrunner - A library for First Tech Challenge that incorperates a ton of really powerful control techniques.  It has built in localization (using drive encoders or dead wheels), trajectory generation, and trajectory following algorithms
11. Path - a set of points that makes up where our robot should be in space.  Usually traversed through by the robots current position.&#x20;
12. Trajectory - a set of position and velocity points, usually indexed by the given time.&#x20;
13. State machine - a way to code your robots that emulates asynchronous behavior but without the side effects.&#x20;
