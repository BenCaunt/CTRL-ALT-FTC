---
description: >-
  Some systems may fail with a simple linear controller such as PID, let's fix
  that!
---

# Gain Scheduling

### Why Gain Scheduling?&#x20;

Controllers like PID and Full State Feedback are inherently Linear.  They compose of simple operations such as multiplication, subtraction, integration, and derivative calculation.  This means that the controllers are easy to design and understand but unfortunately we live in the real world.  In the real world, there is almost **never** such a thing as a linear system.  We are generally able to assume that our system is linear **enough** but sometimes this simply isn't true.  This is where gain scheduling comes into play.&#x20;

### What is Gain Scheduling?&#x20;

Gain scheduling is the idea where we modify our controller parameters relative to where our system is in space.  Imagine a rotating arm on your robot.  You begin by tuning your controller to stabilize in an up write position such as this:

&#x20;![](<../.gitbook/assets/Untitled drawing (5).png>)

You are happy with the performance of having the arm swing up to the vertical position so you proceed to test it in a horizontal position:

![](<../.gitbook/assets/Untitled drawing (6).png>)

You find that after requesting your arm to move to 0° that it ends up settling below your target angle.  Initially you are confused as it worked perfectly for the 90° setpoint but then you remembered that your system is not linear!  As a result of your system not being linear, the linear controller you tuned for one setpoint may not cover the dynamics of the entire **operating region**.&#x20;

{% hint style="info" %}
The **Operating Region** of a system is defined as the space in which the system operates.  If you only operate your system in a small region, it is more likely your linear controller will be adequate. &#x20;
{% endhint %}

A solution to this problem and the inherent concept of gain scheduling is that we can change our controller coefficients depending on where our system is in the operating region.&#x20;

Let's say that this is our current code:&#x20;

```java
// imaginary PID controller and coefficients objects 
PIDCoefficients for90Degrees = new PIDCoefficients(); 
PIDController controller = new PID(for90Dgrees);  


while (loopIsRunning) {
    double armAngle = getArmAngle();
    double targetAngle = getReference();
    double output = controller.calculate(targetAngle, armAngle);
    arm.setPower(output); 
}
```

The code sample above has its PID coefficients tuned to operate at the 90° range.  As we saw with the previous example though it fails whenever we go to 0°. &#x20;

One approach we can take is to split our operating region into two halves, say in the middle at 45°.  Then we will use one set of coefficients above 45° and one set below.&#x20;

```java
// imaginary PID controller and coefficients objects 
PIDCoefficients above45Degrees = new PIDCoefficients(); // tune for above
PIDCoefficients below45Degrees = new PIDCoefficients(); // tune for below 
PIDController controller = new PID(for90Dgrees);  

while (loopIsRunning) {
    double armAngle = getArmAngle();
    double targetAngle = getReference();
    if (armAngle < 45) {
        controller.setCoefficients(below45Degrees);
    } else {
        controller.setCoefficients(above45Degrees); 
    }
    double output = controller.calculate(targetAngle, armAngle);
    arm.setPower(output); 
}
```

This approach works well but doesn't necessarily scale the best, say if you had 10 different operating regions that means you would have to create an if - statement for each one.  That would get messy very quickly.  In addition,  setting your target angle to 45° could theoretically have a bit of extra oscillation as it switches the gains every time it passes over in a single step.&#x20;

An approach to improve this is to use interpolation.&#x20;

A really simple way to accomplish this is to use the FTCLib InterpLUT [(Interpolated Look Up Table)](https://docs.ftclib.org/ftclib/v/v1.2.0/features/util#interplut-interpolated-look-up-table).&#x20;

An interpolated look-up value will take a key (the current angle) and then give you an estimate of what our PID coefficients should be at the given point.  It does this by allowing us to put in a few known values and then it will generate a continuous function to go between them.&#x20;

```java
// look up tables for PID coefficients 
InterpLUT pCoefficients = new InterpLUT();
InterpLUT iCoefficients = new InterpLUT();
InterpLUT dCoefficients = new InterpLUT();

pCoefficients.add(0, KpAt0);
pCoefficients.add(90, KpAt90);

iCoefficients.add(0, KiAt0);
iCoefficients.add(90, KiAt90);

dCoefficients.add(0, KdAt0);
dCoefficients.add(90, KdAt90);

pCoefficients.createLUT();
iCoefficients.createLUT();
dCoefficients.createLUT();

while (loopIsRunning) {
    double armAngle = getArmAngle();
    double targetAngle = getReference();
    
    double Kp = pCoefficients.get(armAngle);
    double Ki = iCoefficients.get(armAngle);
    double Kd = dCoefficients.get(armAngle);
    
    PIDCoefficients coefficients = new PIDCoefficients(Kp, Ki, Kd);
    controller.setCoefficients(coefficients); 
    
    double output = controller.calculate(targetAngle, armAngle);
    arm.setPower(output); 
}


```

{% hint style="success" %}
You will now have a very robust controller powered by gain scheduling!&#x20;
{% endhint %}

{% hint style="info" %}
You aren't limited to just PID.  You can also use this method to adjust your Feedforward or use Full State Feedback instead of PID.&#x20;
{% endhint %}
