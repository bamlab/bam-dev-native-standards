# Gradle

## How to keep version catalog up-to-date ?

Use [dependabot](https://dependabot.com).

### Recommended approach

- Create a `dependencies.gradle` file with all versions and package constants.

  ```groovy
  ext {
  // Version sorted alphabetically
  versions = [
  	android_gradle_plugin              : '7.0.0-beta03',
  	firebase_analytics                 : "18.0.2",
  	flipper                            : "0.74.0",
  	fresco                             : "2.4.0",
  	google_gson                        : "2.8.6"
  	//...
  ]

  runDependencies = [
  	androidx_appcompat                  : "androidx.appcompat:appcompat:${versions.androidx_appcompat}",
  	androidx_biometric                  : "androidx.biometric:biometric:${versions.androidx_biometric}",
  	androidx_browser                    : "androidx.browser:browser:${versions.androidx_browser}",
  	androidx_constraintlayout           : "androidx.constraintlayout:constraintlayout:${versions.androidx_constraintlayout}",
  	androidx_camera                     : "androidx.camera:camera-camera2:${versions.androidx_camera}",
  	androidx_camera_lifecycle           : "androidx.camera:camera-lifecycle:${versions.androidx_camera}",
  	androidx_camera_view                : "androidx.camera:camera-view:${versions.androidx_camera_view}",
  	androidx_core                       : "androidx.core:core-ktx:${versions.androidx_core}"
  	//...
  ]
  }
  ```

- Add this file to all projects in the root `build.gradle`.

  ```groovy

  allprojects {
  project.apply from: rootProject.file("dependencies.gradle")
  }
  ```

- Use it in modules `build.gradle` files.

  ```groovy
   implementation runDependencies.kotlin_std_lib
  implementation runDependencies.koin_android
  implementation runDependencies.koin_android_view_model
  implementation runDependencies.androidx_appcompat
   implementation runDependencies.androidx_core
   ...
  ```

| Pros                                             | Cons                |
| ------------------------------------------------ | ------------------- |
| ➕ one location for all dependencies declaration | ➖ no auto-complete |

### Other approach to try out : Centralized dependency versions with Gradle 7.0

See [the releases notes](https://docs.gradle.org/7.0/release-notes.html).

| Pros                                             | Cons                                    |
| ------------------------------------------------ | --------------------------------------- |
| ➕ one location for all dependencies declaration | ➖ no auto-complete                     |
|                                                  | ➖ toml not supported in Android Studio |
|                                                  | ➖ is it supported by Dependabot ?      |

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
