---
description: Introduction to open loop control in FTC
---

# Introduction to Open Loop Control

## What is Open Loop Control?

Open Loop control simply means giving a specific output without checking it based on our input. A basic example of this which you have almost certainly encountered is that of a time-based autonomous.

‌ A time based autonomous works similarly to the following code:

```java
// start motors at desired speed
FrontLeft.setPower(1);
FrontRight.setPower(1);
BackLeft.setPower(1);
BackRight.setPower(1);
// leave motors running for desired time 
sleep(500)
// stop motors after desired time 
FrontLeft.setPower(0);
FrontRight.setPower(0);
BackLeft.setPower(0);
BackRight.setPower(0);
```

Another example of open-loop control is that of an intake that runs continuously:

```java
// set intake power at max output 
intakeMotor.setPower(1);
```

{% hint style="info" %}
These examples and nearly all further examples assume that you have correctly set up pre-requisite code such as initializing motors. If this is not satisfied, I would recommend setting this up before proceeding.
{% endhint %}

![Simulink Diagram of an Open Loop Motor controller](.gitbook/assets/screen-shot-2021-04-08-at-7.20.35-pm.png)

As you can see, open loop control is an incredibly simple way to get your robot performing actions.  This method does unforunately have a few critical downfalls for certain systems.&#x20;

These downfalls consist of the following:

* Cannot reject disturbances
  * Cannot overcome contact from other robots.  &#x20;
* Energy ineffecient
  * Motors must run at maximum power to utilize all of their maximum torque potential&#x20;
* Consistency will vary as battery voltage changes.    &#x20;

These downfalls really only matter for certain types of systems.  For example, your intake doesnt really need to reject distrubances from the outside world like your drivetrain does.  This means that choosing Open Loop Control vs Closed Loop Control is decided with personal preference and the _**design requirements**_ of your system.

{% hint style="info" %}
**Design Requirements**: What your system needs to accomplish.

Does it need to reject distrubances? Does it need to be fast? Can it be slow? How accurate does it need to be? All of these things  are factored into your systems **Design Requirements**.&#x20;
{% endhint %}

#### Practice Exercises

* Try running the first example on your teams drivetrain
  * How consistent is it?
  * Does the distance vary with the battery voltage?
* Try to think of how to improve upon this method and implement those to see if it works!&#x20;
