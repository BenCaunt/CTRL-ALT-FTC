---
description: In this chapter we will detail how to install the homeostasis library
---

# Installation

### Gradle&#x20;

Navigate to `build.dependencies.gradle` and find `repositories`.&#x20;

In repositories put the maven link to jitpack.io

```
repositories {
    ... // other repositories that are already there
    maven { url 'https://jitpack.io' }

}
```

Then add the dependency:

```
implementation 'com.github.Thermal-Equilibrium:homeostasis-FTC:1.0.5'
```

Now resync your gradle and Homeostasis should be installed!
