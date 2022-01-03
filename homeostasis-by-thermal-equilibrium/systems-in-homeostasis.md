---
description: >-
  Throughout the world, systems containing many parts work together to achieve
  homeostasis; Our robot is no different.
---

# Systems in Homeostasis

### BasicSystem

Basic systems include, a single Estimator, a single Feedback controller, and a single Feedforward Controller.&#x20;

```java
BasicSystem(Estimator estimator, FeedbackController feedbackController,
                FeedforwardController feedforwardController);
```

{% hint style="success" %}
If you wish to not use any of these features that is okay! Just pass in the "No" or "Raw" versions of each interface!&#x20;
{% endhint %}

### PositionVelocitySystem

This is a slightly more complex system that attempts to control both position and velocity at the same time. This uses two feedback controllers, two estimators, and a single feedforward.&#x20;

```
public PositionVelocitySystem(Estimator positionEstimator, 
                              Estimator velocityEstimator,
                              FeedforwardController feedforward,
                               FeedbackController positionFeedback,
                               FeedbackController velocityFeedback);
```

### Example systems&#x20;

#### Basic Motor Control

```java
PIDCoefficients coefficients = new PIDCoefficients(0.3,0.04,0.01);
DoubleSupplier motorPosition = new DoubleSupplier() {
	@Override
	public double getAsDouble() {
		return exampleMotor.getPosition();
	}
};
BasicPID controller = new BasicPID(coefficients); 
NoFeedforward feedforward = new NoFeedforward();
RawValue noFilter = new RawValue(motorPosition); 
BasicSystem system = new BasicSystem(noFilter,controller,feedforward);

while (true) {
	double command = system.update(referencePosition);
}

```

The above example demonstrates the basic syntax for systems.

#### Full State with PID&#x20;

Effectively Full State feedback and Full State Estimation using two PID's and two Kalman Filters.&#x20;

```java
double Q = 0.3;
double R = 3;
int N = 3;
PIDCoefficients posCoefficients = new PIDCoefficients(0.1,0,0);
PIDCoefficients veloCoefficients = new PIDCoefficients(0.1,0,0);
BasicPID posControl = new BasicPID(posCoefficients);
BasicPID veloControl = new BasicPID(veloCoefficients);
DoubleSupplier motorPosition = new DoubleSupplier() {
	@Override
	public double getAsDouble() {
		return exampleMotor.getPosition();
	}
};
DoubleSupplier motorVelocity = new DoubleSupplier() {
	@Override
	public double getAsDouble() {
		return exampleMotor.getVelocity();
	}
};

KalmanEstimator positionFilter = new KalmanEstimator(motorPosition,Q,R,N);
KalmanEstimator velocityFilter = new KalmanEstimator(motorVelocity,Q,R,N); 

FeedforwardCoefficients coefficientsFF = new FeedforwardCoefficients(0.1,0.3,0.001);
BasicFeedforward feedforward = new BasicFeedforward(coefficientsFF);

PositionVelocitySystem system = 
	new PositionVelocitySystem(positionFilter,
	velocityFilter,feedforward,posControl,veloControl);


while (true) {
	// x,v,a is short hand for position, velocity, acceleration targets
	double command = system.update(x,v,a);
}
```

#### Feedforward Only

Sometimes we might not have a sensor available to us and as a result feedforward is our only option!&#x20;

```java
double Kv = 0.1;
double Ka = 0.3;
double Ks = 0.001;
double Kg = 0.1;
double Kcos = 0;
Estimator none = new NoSensor();
NoFeedback none2 = new NoFeedback();
FeedforwardCoefficientsEx coefficientsEx = new FeedforwardCoefficientsEx(Kv,Ka,Ks,Kg,Kcos);
FeedforwardEx controller = new FeedforwardEx(coefficientsEx);
BasicSystem system = new BasicSystem(none,none2,controller); 

while (true) {
    double command = system.update(x,v,a); 
}
```
