# Gradle

## How to speed up Gradle Builds ?

### Enable build cache

Add the following to your gradle properties file `gradle.properties`.

```dotenv
# Cache builds
# By default, build cache is not enabled.
org.gradle.caching=true

# Kapt Worker
# Running kapt tasks in parallel (https://kotlinlang.org/docs/kapt.html#running-kapt-tasks-in-parallel-since-1-2-60).
kapt.use.worker.api=true

# Kapt Compile Avoidance
# https://kotlinlang.org/docs/kapt.html#compile-avoidance-for-kapt-since-1-3-20
kapt.include.compile.classpath=false
```

### Modularize your project

### Try out a shared cache with `gradle/build-cache-node` docker image

- https://hub.docker.com/r/gradle/build-cache-node/
