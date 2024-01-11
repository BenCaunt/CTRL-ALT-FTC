---
description: Our custom approach to PID tuning
---

# SMARTDAMP Algorithm

More content soon.  TLDR: if you have the kV and kA feedforward values.  we can use that as a model for our system. &#x20;

Then, we solve for the poles of the system.  After some math, we arrive at the following:&#x20;

$$
K_D=2\sqrt{K_aK_P}-K_v
$$



This will guarantee a critically damped PID response for a PD controller, given an arbitrary choice of Kp&#x20;

It is recommended that the following inequality is true:&#x20;

$$
K_p \geq \frac{K_v^2}{4K_a}
$$

Otherwise, Kd will be negative and you get a scary non-minimum phase system. &#x20;

### Specifying percent overshoot

A Critically damped system is not necessarily the fastest possible response, sometimes you are okay with a bit of overshoot.  This modified version of the SMARTDAMP algorithm allows you to specify a percent overshoot in addition to your proportional gain to give you your optimal choice for Kd

$$
K_d = 2 \zeta \sqrt{K_a K_p} - K_v
$$



where Zeta is our [dampening ratio](https://en.wikipedia.org/wiki/Damping#Damping\_ratio\_definition).

We can find zeta using the following equation given _PO_ (Percent overshoot)

$$
\zeta =\frac{-\ln{\frac{PO}{100}}}{\sqrt{\pi^2 + \ln^2{\frac{PO}{100}}}}
$$





