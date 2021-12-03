---
description: >-
  This page will help teams that may be new to FTC begin by starting with basic
  motor movement and then working them through the various different ways to
  control such a motor.
---

# FTC Motor Control

{% hint style="info" %}
The Goal of this section is to provide an alternative to the built in RUN TO POSITION mode in the FTC Control System.  We advise teams to use their own, external PID rather than the in built PID because it runs at a faster refresh rate and thus will have much greater stability than the inbuilt controller.  In addition, an external controller allows the flexbility to use one or more custom feedforward controls to further improve the performance of your system. &#x20;
{% endhint %}

## Setting up your development enviornment. &#x20;

Before we start we are going to assume a you have an editor such as Android Studio setup to modify the SDK and that your motors hardware map is completeted.  If not, please check out this [guide](https://gm0.org/en/latest/docs/software/using-android-studio.html) before proceeding. We will also assume for now that you have a basic opmode setup.

### Setting up your motor

For this example, we are going to assume that we are controlling a simple motor such as one that may be on an arm or on your robots drivetrain.  This motor is assumed to have a motor encoder plugged into the Rev expansion hub.  The motor encoder port is generally on the back of the motor and will require a cable that is provided by the motors manufacture.  The purpose of your motor's encoder is that it measures the position of the motor so that we can use control techniques to move it accurately to a desired position.  &#x20;

#### Here you can see how we can create a new motor in our hardware map

```java
public class tutorial extends LinearOpMode {
	// motor declaration, we use the 
	// Ex version as it has velocity measurements
	DcMotorEx motor; 

	@Override
	public void runOpMode() throws InterruptedException {
		// the string is the hardware map name
		motor = hardwareMap.get(DcMotorEx.class, "arm"); 
	
		// use braking to slow the motor down faster 
		motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.BRAKE);
		
		// disables the default velocity control
		// this does NOT disable the encoder from counting, 
		// but lets us simply send raw motor power.
		motor.setMode(DcMotor.RunMode.RUN_WITHOUT_ENCODER);
		waitForStart();
  }
}
```

Above there are a few things happening, for start, on line 3 we are creating an object that is of type DcMotorEx, this is an extended version of the traditional DcMotor class that adds functionality such as measuring the velocity of the motor.  On line **8**, once the user presses init we initialize the motor to the given name that you defined earlier in the hardware map.  If you haven't done this yet you should probably stop and get this out of the way now.  Next on line **12** we **** are setting the motor to use braking,  this allows our motor to decelerate much faster the alternative float mode. &#x20;

On line **14** we are doing something that looks a little bit off but it is what we want.  **RUN WITHOUT** ENCODERS does **NOT** disable the encoder from doing things such as position or velocity measurement but instead lets us just assign a raw motor power to our motor.  The alternative mode, RUN USING ENCODERS simply runs the motor at a speed proportional to its maximum speed, for example setting a power of 0.5 would run the motor at exactly 50% of its speed.  While this may sound great at first, this internal velocity control runs at a slower frequency and will often have less desirable results than using our own custom, external controller.  For this external controller to work properly we should be using RUN WITHOUT ENCODER.

{% hint style="danger" %}
If you glanced over that previous paragraph **dont**.  Read it again, I promise it's important.&#x20;
{% endhint %}

#### Getting the motor moving.&#x20;

Now that we have a motor configured properly we can now start making it move!&#x20;

```java
public class tutorial extends LinearOpMode {

	// motor declaration, we use the 
	// Ex version as it has velocity measurements
	DcMotorEx motor;
	@Override
	public void runOpMode() throws InterruptedException {
		// the string is the hardware map name
		motor = hardwareMap.get(DcMotorEx.class, "arm");

		// use braking to slow the motor down faster 
		motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.BRAKE);

		// disables the default velocity control
		// this does NOT disable the encoder from counting, 
		// but lets us simply send raw motor power.
		motor.setMode(DcMotor.RunMode.RUN_WITHOUT_ENCODER);
		waitForStart();
		// loop that runs while the program should run. 
		while (opModeIsActive()) {
			motor.setPower(0.5);
		}
	}
}

```

In the code snippet above, we added a while loop that runs for the duration of the program and each time the loop runs we are assigning a value at 50% of the motors maximum current. &#x20;

{% hint style="info" %}
If we just wanted to assign a single motor power and be done with it, the above loop would be redundant as assigning motor powers persist until you assign it again using setPower.&#x20;
{% endhint %}

#### Closing the Loop on our motor

In FTC 99% of the time \[citation needed but probably true] we want to drive our motor to a desired position.  A wonderful way to accomplish this in the context of FTC is the PID Controller.  We checked out this controller in a [previous chapter](../the-pid-controller/) and we recommend checking this out to gain an intuition of the controllers structure before continuing with this chapter.&#x20;

When making a PID Controller it is likely a good idea to abstract this into it's own class for easy use.  Here is the basic structure we recommend to start with that you can fill out on your own using the lessons learned from previous chapters: &#x20;

```java
public class PIDController {

	/**
	 * construct PID controller 
	 * @param Kp Proportional coefficient  
	 * @param Ki Integral coefficient 
	 * @param Kd Derivative coefficient 
	 */
	public PIDController(double Kp, double Ki, double Kd) {

	}

	/**
	 * update the PID controller output
	 * @param target where we would like to be, also called the reference
	 * @param state where we currently are, I.E. motor position 
	 * @return the command to our motor, I.E. motor power 
	 */
	public double update(double target, double state) {
		// PID logic and then return the output 
	}
}
```

Once we have our PID controller created we can then use it in our opmode similarly to this:&#x20;

```java
public class tutorial extends LinearOpMode {

	// motor declaration, we use the
	// Ex version as it has velocity measurements
	DcMotorEx motor;
	// create our PID controller, you will need to tune these parameters
	PIDController control = new PIDController(0.05,0,0);
	@Override
	public void runOpMode() throws InterruptedException {
		// the string is the hardware map name
		motor = hardwareMap.get(DcMotorEx.class, "arm");

		// use braking to slow the motor down faster
		motor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.BRAKE);

		// disables the default velocity control
		// this does NOT disable the encoder from counting,
		// but lets us simply send raw motor power.
		motor.setMode(DcMotor.RunMode.RUN_WITHOUT_ENCODER);
		waitForStart();
		// loop that runs while the program should run.
		// position in encoder ticks where we would like the motor to run
		int targetPosition = 100;

		while (opModeIsActive()) {
			// update pid controller 
			double command = control.update(targetPosition,
			                         motor.getCurrentPosition());
			// assign motor the PID output 
			motor.setPower(command);
		}
	}
}
```

{% hint style="success" %}
You now have an opmode that, using your PID controller will drive to a desired position!&#x20;
{% endhint %}

{% hint style="info" %}
There are a few places we can go to now to improve this

1. you can implement one or more [feedforward](../feedforward-control.md) controllers to enhance the systems stability
2. You can use .getVelocity() instead of get position and turn this into a velocity controller if your heart so desires.  (This is why we use DcMotorEx).&#x20;
3. You can implement a [finite state machine](https://gm0.org/en/latest/docs/software/finite-state-machines.html) paradigm so that your PID controller will continue to update &#x20;
4. Every time you get the [urge to use threads](https://gm0.org/en/latest/docs/software/control-system-internals.html) ... simply don't and see step three.&#x20;
   1. The reason for this according to Game Manual 0 is that, "LynxCommands being blocking (and more specifically a per-bus master lock being present) means that multithreading hardware calls is at best not helpful and typically harmful to performance" ([Control System Internals](https://gm0.org/en/latest/docs/software/control-system-internals.html)).
{% endhint %}



####



&#x20;****&#x20;
