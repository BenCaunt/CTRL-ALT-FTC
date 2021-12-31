---
description: 'Introduction to the Homestasis library by FTC #19376 Thermal Equilibrium'
---

# What is Homeostasis?

Homeostasis is a library in development by FTC #19376 Thermal Equilibrium. Homeostasis aims to provide an easy way to rapidly implement advanced control techniques with as slight a headache as possible. The end goal for Homeostasis is to provide a one size fits all solution to control and state estimation in First Tech Challenge.&#x20;

We identified that, for the most part, one could very elegantly abstract most code for FTC control systems away, and with Homeostasis, we aim to provide a controls library that plays nicely with the majority of programming paradigms used in FTC, such as that of the [state machine](https://gm0.org/en/latest/docs/software/finite-state-machines.html) and [command-based](https://docs.ftclib.org/ftclib/command-base/command-system) paradigms.&#x20;

Currently homestasis provides the following features:

* [Implementations of many controllers](https://www.ctrlaltftc.com/homeostasis-by-thermal-equilibrium/included-controllers)
  * [PID Control](https://www.ctrlaltftc.com/homeostasis-by-thermal-equilibrium/included-controllers#pidex)
  * [Full State Feedback](https://www.ctrlaltftc.com/homeostasis-by-thermal-equilibrium/included-controllers#full-state-feedback-control)
  * [Bang Bang Control](https://www.ctrlaltftc.com/homeostasis-by-thermal-equilibrium/included-controllers#bang-bang-control)
  * [Basic](https://www.ctrlaltftc.com/homeostasis-by-thermal-equilibrium/included-controllers#basicfeedforward), [Gravity, and Arm Feedforward](https://www.ctrlaltftc.com/homeostasis-by-thermal-equilibrium/included-controllers#feedforwardex)
* Estimation algorithms to improve sensor readings:
  * Low Pass Filter
  * A custom Least Squares Regression + Kalman Filter algorithm
* &#x20;Many useful utilies such as the WPILib Motion Profile and functions to deal with angles.&#x20;
* Systems to easily utilize the afformentioned algorithms in a unified way.
  * BasicSystem
    * A combination of an Estimator, Feedback, and Feedforward Controller
  * &#x20;PositionVelocitySystem
    * A combination of (2) Estimators (one for position, one for velocity), (2) feedback controllers (one for position, one for velocity) and (1) Feedforward.&#x20;
