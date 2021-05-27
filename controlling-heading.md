---
description: >-
  One common use of PID in FTC is turning your robot to a desired heading
  however this will result in a few modifications being made.
---

# Angles and Error Calculations

Many may find that when attempting to use your PID controller to turn your robot to the desired angle that your robot sometimes keeps spinning or taking the longest possible way to arrive at this target angle. 

This issue arises from the simple nature that angles do not go on forever and instead wrap around. Imagine our calculation for error that we did in previous chapters: error = reference - state  
  
 Where the reference is where we would like to be, and the state is where we currently are. This method is fantastic for measurements that continue forever, such as that of an encoder but has a significant downfall in wrapping measurements. ‌

Let us say, for example, that our robot is at 1 degree, and we would like to turn our robot to 359 degrees. We can calculate the error between these two as 359 - 1 = 358. This result is very suboptimal as out of the two possible ways the robot could have turned, it chose the longest possible way, turning nearly entirely around the other way. Accounting for angle wrap can solve this with just a little bit of code:

```java
public void angleWrap(double radians) {

    while (radians > PI) {
        radians -= 2 * PI;
    }  
    while (radians < -PI) {
        radians += 2 * PI;
    }

    // keep in mind that the result is in radians
    return radians;

}
```

Example borrowed from: [ FTC Programming: Pure Pursuit Tutorial 1 by FTC 11115 Gluten Free ](https://www.youtube.com/watch?v=3l7ZNJ21wMo)

We can then calculate our error as the following:

```java
Math.todegrees(angleWrap(Math.toradians(359 - 1)));
```

which then performs the following operations: 

359 - 1 = 358  
358 &gt; 180

358 - 360 = -2 

new corrected angle = -2

Using this angle wrap method will ensure that your PID controller has your robot turn in the desired direction and will prevent numerous issues such as your robot potentially spinning infinitely in circles. There are more efficient ways to implement this, such as using the modulus operator, but that is something up to you to do for yourself.

{% hint style="success" %}
AngleUnit.normalize in the FTC SDK accomplishes this aswell.
{% endhint %}

