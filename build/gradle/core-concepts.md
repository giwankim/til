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

```
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
├── subproject-b
│   ├── build.gradle.kts
│   └── src
```

- `gradle` directory stores wrapper files and version catalog for dependency management
- `settings.gradle.kts` define a root project name and subprojects
- `build.gradle.kts` files are the build scripts of the two subprojects


[^1]: [Gradle User Manual](https://docs.gradle.org/current/userguide/userguide.html)
