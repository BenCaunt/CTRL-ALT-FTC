---
description: 'Introduction to the Homestasis library by FTC #19376 Thermal Equilibrium'
---

# What is Homeostasis?

Homeostasis is a library in development by FTC \#19376 Thermal Equilibrium. Homeostasis aims to provide an easy way to rapidly implement advanced control techniques with as slight a headache as possible. The end goal for Homeostasis is to provide a one size fits all solution to control and state estimation in First Tech Challenge. 

We identified that, for the most part, one could very elegantly abstract most code for FTC control systems away, and with Homeostasis, we aim to provide a controls library that plays nicely with the majority of programming paradigms used in FTC, such as that of the [state machine](https://gm0.org/en/latest/docs/software/finite-state-machines.html) and [command-based](https://docs.ftclib.org/ftclib/command-base/command-system) paradigms. 

Currently homestasis provides the following features:

* Template classes to represent dynamical subsystems on your robot.
* System characterization methods to identify static friction, maximum velocity, and maximum acceleration of any arbitrary system. 
* Full state feedback control for multiple input single output velocity / position systems. 
* Nonlinear PID controller as described in the [Practical Improvements to the PID Controller](https://www.ctrlaltftc.com/practical-improvements-to-pid) selection.
* Prebuilt kalman filters for common applications.
  * Velocity estimation from position sensors
  * Attitude estimation from combining heading sensors.  



