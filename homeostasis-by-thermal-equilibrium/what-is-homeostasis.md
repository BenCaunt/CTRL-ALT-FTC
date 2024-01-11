---
description: 'Introduction to the Homestasis library by FTC #19376 Thermal Equilibrium'
---

# What is Homeostasis?

Homeostasis is a library in development by FTC #19376 Thermal Equilibrium. Homeostasis aims to provide an easy way to rapidly implement advanced control techniques with as slight a headache as possible. The end goal for Homeostasis is to provide a one size fits all solution to control and state estimation in First Tech Challenge. 

We identified that, for the most part, one could very elegantly abstract most code for FTC control systems away, and with Homeostasis, we aim to provide a controls library that plays nicely with the majority of programming paradigms used in FTC, such as that of the [state machine](https://gm0.org/en/latest/docs/software/finite-state-machines.html) and [command-based](https://docs.ftclib.org/ftclib/command-base/command-system) paradigms. 

Currently homestasis provides the following features:

* Implementations of many controllers
  * PID Control
  * Full State Feedback
  * Bang Bang Control
  * Basic, Gravity, and Arm Feedforward
* Estimation algorithms to improve sensor readings:
  * Low Pass Filter
  * Least Squares + Kalman Filter
* Many useful utilities such as the WPILib Motion Profile and functions to deal with angles. 
* Systems to easily utilize the aforementioned algorithms in a unified way.
  * BasicSystem
    * A combination of an Estimator, Feedback, and Feedforward Controller
  *  PositionVelocitySystem
    * A combination of (2) Estimators (one for position, one for velocity), (2) feedback controllers (one for position, one for velocity) and (1) Feedforward. 

{% hint style="danger" %}
Currently homeostasis is in Beta and while in many cases it's algorithms have been confirmed to work very well, in some edge cases they could still potentially fail. Please let us know or create a github issue / PR if you encounter any issues at: [https://github.com/Thermal-Equilibrium/homeostasis-FTC](https://github.com/Thermal-Equilibrium/homeostasis-FTC)
{% endhint %}
