# Core Concepts

![gradle](../../assets/gradle.png)


## Basics

Gradle automates building, testing, and deployment of software from information in build scripts.

### Core concepts

#### Projects

A Gradle project is a piece of software that can be build, such as an application or a library.

Single project builds include a single project called the root project.

Multi-project builds include one root project and any number of subprojects.

#### Build scripts

Build scripts detail to Gradle what steps to take to build the project.

#### Dependency management

Dependency management is an automated technique for declaring and resolving external resources required by a project.

#### Tasks

Tasks are a basic unit of work such as compiling code or running tests.

Each project contains one or more tasks defined inside a build script or a plugin.

#### Plugins

Plugins are used to extend Gradle's capability and optionally contribute tasks to a project.

### Project structure

A Gradle project will look similar to the following:

```bash
project
├── gradle
│   ├── libs.versions.toml
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── settings.gradle.kts
├── subproject-a
│   ├── build.gradle.kts
│   └── src
└── subproject-b
    ├── build.gradle.kts
    └── src
```

- `gradle` directory stores wrapper files and version catalog for dependency management
- `settings.gradle.kts` define a root project name and subprojects
- `build.gradle.kts` files are the build scripts of the two subprojects


## Gradle Wrapper

The recommended way to execute any Gradle build is with the Gradle Wrapper. The *Wrapper* script invokes a declared version of Gradle, downloading it beforehand if necessary.

```bash
.
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar  
│       └── gradle-wrapper.properties   
├── gradlew 
└── gradlew.bat 
```

- `gradle-wrapper.jar`: This is a small JAR file that contains the Gradle Wrapper code. It is responsible for downloading and installing the correct version of Gradle for a project if it's not already installed.
- `gradle-wrapper.properties`: This file contains the configuration properties for the Gradle Wrapper, such as the distribution URL and the distribution type.
- `gradlew`: This is a shell script that acts as a wrapper around `gradle-wrapper.jar`. It is used to execute Gradle tasks on Unix-based systems without needing to manually install Gradle.
- `gradlew.bat`: Batch script that serves the same purpose as `gradlew` but on Windows system.

To view or update the Gradle version, use the command line and do **not** edit the wrapper files manually:
```bash
$ ./gradlew --version
$ ./gradlew wrapper --gradle-version 8.10.2
```


## Settings File

Settings file is the entry point of every Gradle project.
Primary purpose of the settings file is to add subprojects to the build.
- For single-project builds, the setting file is optional.
- For multi-project builds, the settings file is mandatory and declares all subprojects.

### Settings script

`setting.gradle.kts` or `setting.gradle` is typically found in the root directory of the project and it defines the project name and defines the structure of the project by including subprojects, if there are any.

```kotlin
rootProject.name = "root-project"

include("sub-project-a")
include("sub-project-b")
include("sub-project-c")
```


[^1]: [Gradle User Manual](https://docs.gradle.org/current/userguide/userguide.html)
