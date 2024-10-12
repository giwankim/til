# Authoring builds

## 1. Gradle Directories

Gradle uses two main directories to perform and manage its work: Gradle User Home directory and the Project Root directory.

![gradle directories](../../assets/gradle-directories.png)

### Gradle User Home directory

By default, the Gradle User Home (`~/.gradle` or `C:\Users\<USERNAME>\.gradle`) stores global configuration properties, initialization scripts, caches, and log files.

It can be set with the environment variable `GRADLE_USER_HOME`.

It is roughly structured as follows:

```bash
├── caches                  
│   ├── 4.8                     
│   ├── 4.9                     
│   ├── ⋮
│   ├── jars-3                  
│   └── modules-2               
├── daemon 
│   ├── ⋮
│   ├── 4.8
│   └── 4.9
├── init.d                  
│   └── my-setup.gradle
├── jdks                    
│   ├── ⋮
│   └── jdk-14.0.2+12
├── wrapper
│   └── dists                   
│       ├── ⋮
│       ├── gradle-4.8-bin
│       ├── gradle-4.9-all
│       └── gradle-4.9-bin
└── gradle.properties
```

- `caches` is the global cache directory containing version specific caches to support incremental builds.
- `caches/jars-3` and `caches/modules-2` are shared caches for artifacts of dependencies.
- `daemon`: registry and logs for the [Gradle Daemon](https://docs.gradle.org/current/userguide/gradle_daemon.html#gradle_daemon).
- `init.d`: global [initialization scripts](https://docs.gradle.org/current/userguide/init_scripts.html#init_scripts)
- `jdks`: JDKs downloaded by the toolchain support.
- `wrapper/dists`: distribution downloaded by the [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html#gradle_wrapper_reference)
- Global [Gradle configuration properties](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties)

Consult the [Gradle Directories reference](https://docs.gradle.org/current/userguide/directory_layout.html#dir:gradle_user_home) to learn more.

### Project Root directory

The anatomy of a typical project root directory looks as follows:
```bash
├── .gradle
│   ├── 4.8
│   ├── 4.9
│   └── ⋮
├── build
├── gradle
│   └── wrapper
├── gradle.properties
├── gradlew
├── gradlew.bat     
├── settings.gradle.kts
├── subproject-one    
|   └── build.gradle.kts
├── subproject-two    
|   └── build.gradle.kts
└── ⋮
```

## 2. Multi-Project Builds

## 3. Gradle Build Lifecycle

## 4. Writing Settings Files

## 5. Writing Build Scripts

## 6. Using Tasks