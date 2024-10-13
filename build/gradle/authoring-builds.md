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

Gradle supports _multi-project_ builds. This is sometimes referred to as a multi-module project. Gradle refers to modules as subprojects.

### Multi-Project standards

Gradle community has two standards for multi-project build structures:

1. `buildSrc` - `buildSrc` is a subproject-like directory at the Gradle project root containing all the build logic.
2. Composite builds - a build that includes other builds where `build-logic` is a build directory at the Gradle project root containing reusable build logic.

![alt text](https://docs.gradle.org/current/userguide/img/multi-project-standards.png)

#### 1. Multi-Project Builds using `buildSrc`

For example, a build that has many modules called `mobile-app`, `web-app`, `api`, `lib`, and `documentation` could be structured as follows:

```bash
.
├── gradle
├── gradlew
├── settings.gradle.kts
├── buildSrc
│   ├── build.gradle.kts
│   └── src/main/kotlin/shared-build-conventions.gradle.kts
├── mobile-app
│   └── build.gradle.kts
├── web-app
│   └── build.gradle.kts
├── api
│   └── build.gradle.kts
├── lib
│   └── build.gradle.kts
└── documentation
    └── build.gradle.kts
```

The modules have dependencies between them such as `web-app` and `mobile-app` depending on `lib`. This means that in order for Gradle to build `web-app` or `mobile-app`, it must build `lib` first.

`buildSrc` is automatically recognized by Gradle. It is a good place to define and maintain shared configuration or imperative build logic, such as custom tasks or plugins.

If the `java` plugin is applied to the `buildSrc` project, the compiled code from `buildSrc/src/main/java` is put in the classpath fo the root build script, making it available to any subproject in the build.

#### 2. Composite Builds

Composite Builds, also referred to as _included builds_, are best for sharing logic between builds (_not subprojects_) or isolating access to shared build logic (i.e. convention plugins).

The logic in `buildSrc` from the previous example has been turned into a project that contains plugins and be published and worked on independently of the root project build.

The plugin is moved to its own build called `build-logic` with a build script and settings file:

```bash
.
├── gradle
├── gradlew
├── settings.gradle.kts
├── build-logic
│   ├── settings.gradle.kts
│   └── conventions
│       ├── build.gradle.kts
│       └── src/main/kotlin/shared-build-conventions.gradle.kts
├── mobile-app
│   └── build.gradle.kts
├── web-app
│   └── build.gradle.kts
├── api
│   └── build.gradle.kts
├── lib
│   └── build.gradle.kts
└── documentation
    └── build.gradle.kts
```

The root settings file includes the entire `build-logic` build:

```kotlin
pluginManagement {
  includeBuild("build-logic")
}
include("mobile-app", "web-app", "api", "lib", "documentation")
```

## 3. Gradle Build Lifecycle

The build author defines the tasks and dependencies between tasks. Gradle guarantees that these tasks will execute in order of their dependencies.

For example, if the project tasks include `build`, `assemble`, `createDocs`, the build scripts can ensure that they are executed in the order `build` -> `assemble` -> `createDoc`.

### Task Graphs

Gradle builds the task graph before executing any task.

Across all projects in the build, tasks form a Directed Acyclic Graph.

Here is a partial task graph for a standard Java build, with dependencies between tasks represented as arrows:

```mermaid
  flowchart TD
    build([build]) --> check([check]) --> test([test])
    assemble([assemble]) --> jar([jar])
    build --> assemble
    jar --> classes([classes])
    classes --> compileJava([Compile Java])
    classes --> processResources([processResources])
```

Both plugins and build scripts contribute to the task graph via the task dependency mechanism and annotated inputs/outputs.

### Build Phases

A Gradle build has three distinct phases.

```mermaid
flowchart LR
  Initialization["1 Initialization Phase"] --> Configuration[2 Configuration Phase] --> Execution[3 Configuration Phase]
```

Gradle runs these phases in order:

#### Phase 1. Initialization

- Detects the `settings.gradle.kts` file
- Creates a `Settings` instance
- Evaluates the settings file to determine which projects (and included builds) make up the build.
- Creates a `Project` instance for every project.

#### Phase 2. Configuration

- Evaluates the build scripts, `build.gradle.kts`, of every project participating in the build.
- Creates a task graph for requested tasks.

#### Phase 3. Execution

- Schedules and executes the selected tasks.
- Dependencies  tasks determine execution order.
- Execution of tasks can occur in parallel.

![build phases](https://docs.gradle.org/current/userguide/img/build-lifecycle-example.png)

## 4. Writing Settings Files

## 5. Writing Build Scripts

## 6. Using Tasks
