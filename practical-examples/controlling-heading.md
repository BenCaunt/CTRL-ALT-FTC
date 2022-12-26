---
description: >-
  A very common use case for PID controllers in FTC is turning your robot to a
  desired heading. However, a few modifications are necessary.
---

# Dealing with Angles

Many may find that attempting to use your PID controller to turn your robot to a desired angle will cause your robot to turn the longest way possible or spin unnecessary rotations.

This issue arises from the simple nature that angles do not go on forever and instead wrap around. Imagine our calculation for error that we did in previous chapters: `error = reference - state`

`reference` is where we would like to be, and the `state` is where we currently are. This method is fantastic for measurements that continue forever, such as that of an encoder, but fails to perform properly for wrapping measurements.

For example, let's assume that our robot is facing an angle of 1 degree. We would like to turn our robot to face 359 degrees. We can calculate the error between these two as `359 - 1 = 358`. This result is very suboptimal; out of the two possible ways the robot could have turned, it chose the longest possible way, turning nearly a full rotation the other way. Accounting for angle wrap can solve this with just a little bit of code:

```java
// This function normalizes the angle so it returns a value between -180° and 180° instead of 0° to 360°.
public double angleWrap(double radians) {

    while (radians > Math.PI) {
        radians -= 2 * Math.PI;
    }  
    while (radians < -Math.PI) {
        radians += 2 * Math.PI;
    }

    // keep in mind that the result is in radians
    return radians;
}
```

Example borrowed from: [ FTC Programming: Pure Pursuit Tutorial 1 by FTC 11115 Gluten Free ](https://www.youtube.com/watch?v=3l7ZNJ21wMo)

We can then calculate our error as the following:

```java
Math.toDegrees(angleWrap(Math.toRadians(359 - 1)));
```

which then performs the following operations:

> 359 - 1 = 358
>
> 358 &gt; 180
>
> 358 - 360 = -2
>
> new corrected angle = -2

Using this angle wrap method will ensure that your PID controller turns in the desired direction and will prevent numerous issues such as your robot potentially spinning infinitely in circles. There are more efficient ways to implement this, such as using the modulus operator, but that is something up to you to do for yourself. See [Road Runner's angle utility](https://github.com/acmerobotics/road-runner/blob/96a59ae121806780fd2d4086f388309a7870caa4/core/src/main/kotlin/com/acmerobotics/roadrunner/util/Angle.kt) for one such example.

{% hint style="success" %}
The FTC SDK provides the [AngleUnit\#normalize\(radians\)](https://github.com/OpenFTC/Extracted-RC/blob/4f328d0b0f14dffeba68356a4c0170b9b2074619/RobotCore/src/main/java/org/firstinspires/ftc/robotcore/external/navigation/AngleUnit.java#L197) function that performs exactly the same operation as the our `angleWrap` above.
{% endhint %}

