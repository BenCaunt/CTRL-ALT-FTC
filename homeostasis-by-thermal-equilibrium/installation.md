---
description: In this chapter we will detail how to install the homeostasis library
---

# Installation

### Gradle

Navigate to `build.dependencies.gradle` and find `repositories`.

In repositories put the maven link to jitpack.io

```groovy
repositories {
    ... // other repositories that are already there
    maven { url 'https://jitpack.io' }

}
```

Then add the dependency:

```groovy
implementation 'com.github.Thermal-Equilibrium:homeostasis-FTC:1.0.8'
```

Now resync your gradle and Homeostasis should be installed!
