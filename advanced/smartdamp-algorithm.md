---
description: Our custom approach to PID tuning
---

# SMARTDAMP Algorithm

More content soon.  TLDR: if you have the kV and kA feedforward values.  we can use that as a model for our system. &#x20;

Then, we solve for the poles of the system.  After some math, we arrive at the following:&#x20;

$$
K_D=2\sqrt{K_aK_P}-K_a
$$

This will guarantee a critically damped PID response for a PD controller, given most arbitrary choices of Kp. &#x20;

It is recommended that the following inequality is true:&#x20;

$$
K_p \geq \frac{K_v^2}{4K_a}
$$

Otherwise, Kd will be negative and you get a scary non-minimum phase system. &#x20;

This article will be fleshed out more very soon!

