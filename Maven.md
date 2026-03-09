

1. ✅ What Maven _actually is_
    
2. ✅ The **Lifecycle** (this is the spine)
    
3. ✅ **Goals & Plugins** (the engine)
    
4. ✅ **Command structure** (how everything maps together)
    

No fluff. This is senior-level mental model.

---

# ✅ 1️⃣ WHAT MAVEN ACTUALLY IS (NOT A COMMAND RUNNER)

Most people think:

> “Maven is a tool that runs `mvn clean install`.”

Wrong mental model.

✅ **Correct model:**

> Maven is a **lifecycle-driven build orchestrator** that executes **plugin goals** at well-defined **phases**, based on rules defined in `pom.xml`.

In simpler words:

- You never directly “build Java”
    
- You **ask Maven to reach a phase**
    
- Maven:
    
    - Figures out which plugins are bound to that phase
        
    - Executes all their goals **in order**
        
    - Handles dependencies automatically
        

You don’t run “build logic”.  
You run **phases**, and Maven does the rest.

---

# ✅ 2️⃣ THE MAVEN LIFECYCLE (THE BACKBONE)

A **Lifecycle = ordered sequence of phases**.

There are **3 built-in lifecycles**:

|Lifecycle|Purpose|
|---|---|
|`default`|Build (compile, test, package, install, deploy)|
|`clean`|Cleanup (`target/`)|
|`site`|Documentation|

You mostly live in **`default` and `clean`**.

---

## ✅ 2.1 THE DEFAULT LIFECYCLE (CORE BUILD PIPELINE)

This is the **real spine of Maven**:

```
validate
compile
test
package
verify
install
deploy
```

Let’s decode each like an engineer:

---

### 🔹 `validate`

> “Is the project even sane?”

- Checks:
    
    - `pom.xml` exists
        
    - All required config is present
        
- ❌ No compilation yet
    

---

### 🔹 `compile`

> “Compile main source code”

- Compiles:
    
    ```
    src/main/java → target/classes
    ```
    
- ❌ No tests
    
- ❌ No JAR
    

---

### 🔹 `test`

> “Run unit tests”

- Compiles:
    
    - Main code ✅
        
    - Test code ✅
        
- Runs:
    
    - `src/test/java`
        
- Uses:
    
    - `maven-surefire-plugin`
        

---

### 🔹 `package`

> “Turn compiled code into a distributable artifact”

Creates:

- `jar` → for Spring Boot / libraries
    
- `war` → for servlet containers
    

Output:

```
target/app.jar
```

This is where your **application is born**.

---

### 🔹 `verify`

> “Run integration-level checks”

- Runs:
    
    - Integration tests
        
    - Code quality rules
        
- Mostly used in CI pipelines
    

---

### 🔹 `install`

> “Put the built artifact into my local Maven cache”

Copies JAR into:

```
~/.m2/repository
```

Now other local projects can depend on it.

---

### 🔹 `deploy`

> “Send artifact to remote repository”

Uploads to:

- Nexus
    
- Artifactory
    
- S3
    
- Company repo
    

Used in:

- CI/CD
    
- Releases
    

---

## ✅ 2.2 THE CLEAN LIFECYCLE

This is separate:

```
pre-clean → clean → post-clean
```

### 🔹 `clean`

Deletes:

```
target/
```

No compilation. Just cleanup.

---

# ✅ 3️⃣ PLUGINS & GOALS (THE ENGINE UNDER THE HOOD)

Here’s the **most important truth about Maven**:

> Maven itself does **almost nothing**.  
> ✅ Everything real is done by **plugins**.

---

## ✅ 3.1 Plugin

A **plugin** is a Java program that knows how to:

- Compile Java
    
- Run tests
    
- Create JARs
    
- Run Spring Boot
    
- Generate docs
    
- Upload artifacts
    

Examples:

|Plugin|Purpose|
|---|---|
|`maven-compiler-plugin`|Compiles Java|
|`maven-surefire-plugin`|Runs tests|
|`maven-jar-plugin`|Builds jar|
|`spring-boot-maven-plugin`|Runs Spring apps|
|`maven-deploy-plugin`|Uploads artifacts|

---

## ✅ 3.2 Goal

A **goal** is a single task inside a plugin.

Examples:

|Plugin|Goal|Meaning|
|---|---|---|
|compiler|`compile`|Compile sources|
|surefire|`test`|Run unit tests|
|jar|`jar`|Build JAR|
|spring-boot|`run`|Run app|

Syntax:

```
plugin:goal
```

Example:

```bash
mvn spring-boot:run
```

This **does NOT run the Maven lifecycle**.  
It runs **that one goal directly**.

---

## ✅ 3.3 Phase vs Goal (CRITICAL DIFFERENCE)

|Phase|Goal|
|---|---|
|Part of lifecycle|Part of plugin|
|Generic step|Concrete action|
|`compile`|`compiler:compile`|
|`test`|`surefire:test`|

✅ A **phase triggers one or more goals automatically**.

Example:

When you run:

```bash
mvn test
```

Maven internally runs:

- compiler:compile
    
- compiler:testCompile
    
- surefire:test
    

You didn’t call these goals.  
The **lifecycle wiring did**.

---

# ✅ 4️⃣ COMMAND STRUCTURE (HOW TO READ ANY MVN COMMAND)

There are only **two valid command patterns** in Maven:

---

## ✅ 4.1 Phase-based command

```bash
mvn <phase>
```

Examples:

|Command|Meaning|
|---|---|
|`mvn compile`|Run everything up to compile|
|`mvn test`|Run everything up to test|
|`mvn package`|Build JAR|
|`mvn install`|Build + put in `.m2`|
|`mvn deploy`|Upload|

⚠️ When you run a phase, Maven runs **ALL previous phases automatically**.

Example:

```bash
mvn install
```

Runs:

```
validate → compile → test → package → verify → install
```

---

## ✅ 4.2 Plugin-goal command

```bash
mvn <plugin>:<goal>
```

Examples:

```bash
mvn spring-boot:run
mvn dependency:tree
mvn clean:clean
```

This:

- ❌ Does NOT run the lifecycle
    
- ✅ Runs only that goal
    

---

## ✅ 4.3 Combined Command (Most Real-World Usage)

```bash
mvn clean install
```

This means:

1. Run `clean` lifecycle → delete target
    
2. Then run `install` phase of default lifecycle
    

---

# ✅ 5️⃣ WHERE CONFIGURATION LIVES (`pom.xml` MENTAL MAP)

Your `pom.xml` has **4 major sections** that matter for builds:

---

## 🔹 1. `<dependencies>`

Used for:

- Libraries
    
- Frameworks
    

Pulled into:

- Compile classpath
    
- Test classpath
    
- Runtime
    

---

## 🔹 2. `<build><plugins>`

Used for:

- Overriding plugin behavior
    

Example:

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.11.0</version>
  <configuration>
    <source>17</source>
    <target>17</target>
  </configuration>
</plugin>
```

---

## 🔹 3. `<properties>`

Central version control:

```xml
<java.version>17</java.version>
```

---

## 🔹 4. `<profiles>`

Used for:

- Dev vs prod builds
    
- Feature toggles
    

Activated via:

```bash
mvn package -Pprod
```

---

# ✅ 6️⃣ SUPER IMPORTANT CONCEPT: DEPENDENCY SCOPE

|Scope|Used For|
|---|---|
|`compile`|Default|
|`test`|Only tests|
|`provided`|Container provides|
|`runtime`|Needed at runtime only|

This directly affects:

- What goes into your JAR
    
- What is available during testing
    

---

# ✅ 7️⃣ WHY PEOPLE GET CONFUSED WITH MAVEN

Because they mix these 3 layers:

1. **Lifecycle** → phases
    
2. **Plugins** → tools
    
3. **Goals** → actions
    

Once you separate them, Maven becomes predictable instead of mystical.

---

# ✅ 8️⃣ INTERVIEW-LEVEL ONE-LINERS

You can now safely say:

- ✅ “Maven is a lifecycle-based build tool driven by plugins.”
    
- ✅ “Phases are part of a lifecycle; goals belong to plugins.”
    
- ✅ “Running a phase executes all earlier phases automatically.”
    
- ✅ “Plugins provide the real implementation; Maven just orchestrates.”
    
- ✅ “`install` puts the artifact into the local `.m2` repository.”
    

These are **senior-correct**.

---

# ✅ 9️⃣ 60-SECOND MAVEN RECAP

- Maven works on **lifecycles**
    
- Lifecycles have **phases**
    
- Phases trigger **plugin goals**
    
- Plugins do **all real work**
    
- `pom.xml` wires everything together
    


Think of Maven like a **factory pipeline**.

There are three layers:

```
Lifecycle
   ↓
Phases
   ↓
Plugin Goals
```

Let's unpack them carefully.

---

# 1. Lifecycle (The Big Workflow)

A **lifecycle** is the **entire build process**.

It defines **the sequence of steps required to build a project**.

Maven has **3 built-in lifecycles**:

|Lifecycle|Purpose|
|---|---|
|**default**|build and deploy project|
|**clean**|remove previous build artifacts|
|**site**|generate project documentation|

Most of the time you use the **default lifecycle**.

---

## Default Lifecycle

This lifecycle contains many phases.

Simplified view:

```
validate
compile
test
package
verify
install
deploy
```

Each phase represents a **stage of the build**.

Example:

```
compile → convert Java source to bytecode
package → create jar
install → put jar in local repository
```

So lifecycle = **overall build pipeline**.

---

# 2. Phases (Steps Inside Lifecycle)

A **phase** is a **step in the lifecycle**.

For example:

```
compile
```

means:

> "Compile the source code."

Another phase:

```
package
```

means:

> "Create a distributable artifact."

Important rule:

### When you run a phase, Maven runs all earlier phases automatically.

Example command:

```
mvn package
```

Maven runs:

```
validate
compile
test
package
```

in order.

This guarantees builds happen **in a consistent order**.

---

# 3. Goals (Actual Tasks Executed)

Now here's the key idea.

A **phase itself does nothing**.

Instead, it **triggers plugin goals**.

A **goal** is the **actual task that performs work**.

Example:

```
compile phase
```

triggers:

```
maven-compiler-plugin:compile
```

This goal runs:

```
javac
```

and produces `.class` files.

---

# Example Mapping

|Phase|Plugin Goal|
|---|---|
|compile|compiler:compile|
|test|surefire:test|
|package|jar:jar|

So when you run:

```
mvn package
```

Maven internally executes something like:

```
compiler:compile
surefire:test
jar:jar
```

These goals actually perform the work.

---

# Example — What Happens During `mvn compile`

Command:

```
mvn compile
```

Execution:

```
Lifecycle: default
Phase: compile
```

Maven triggers goal:

```
maven-compiler-plugin:compile
```

That goal runs the Java compiler:

```
javac
```

Output:

```
target/classes/*.class
```

---

# Example — What Happens During `mvn test`

Command:

```
mvn test
```

Maven runs phases:

```
validate
compile
test
```

The test phase triggers:

```
maven-surefire-plugin:test
```

Surefire plugin:

1. launches JVM
    
2. finds test classes
    
3. runs JUnit tests
    

---

# Example — What Happens During `mvn package`

Phases executed:

```
validate
compile
test
package
```

The package phase triggers:

```
maven-jar-plugin:jar
```

Which creates:

```
target/app.jar
```

---

# Visualizing the Whole System

```
Lifecycle (big process)
        │
        ▼
Phases (steps)
        │
        ▼
Plugin Goals (actual tasks)
```

Example:

```
default lifecycle
        │
        ▼
compile phase
        │
        ▼
compiler:compile goal
        │
        ▼
javac runs
```

---

# Important Idea

You almost **never run goals directly**.

Instead you run phases.

Example:

Normal usage:

```
mvn package
```

Direct goal execution (rare):

```
mvn compiler:compile
```

Why?

Because phases ensure **the correct build order**.

---

# Why Maven Was Designed This Way

The idea was:

Developers shouldn't need to know **how to build a project**, only **what stage to run**.

Example:

```
mvn test
```

means:

> "Run tests."

You don't need to remember:

```
compile first
set classpath
launch JUnit
```

Maven standardizes the process.

---

# Real Example With Spring Boot

When you run:

```
mvn package
```

Spring Boot adds its own goal:

```
spring-boot:repackage
```

So the package phase executes:

```
jar:jar
spring-boot:repackage
```

Result:

```
target/app.jar
```

with all dependencies embedded.

---

# Quick Summary

Lifecycle = **full build workflow**

```
default lifecycle
```

Phases = **steps in that workflow**

```
compile
test
package
```

Goals = **actual work executed by plugins**

```
compiler:compile
surefire:test
jar:jar
```

---

# One Small Example That Makes It Click

Think of it like cooking.

Lifecycle:

```
make pizza
```

Phases:

```
prepare dough
add toppings
bake
```

Goals (actual actions):

```
mix flour
add cheese
turn oven on
```

Phases describe **what stage you're in**, goals describe **the actual work done**.

---

# Maven Architecture Notes

## 1. What Maven Is

Maven is primarily:

- a **build lifecycle manager**
    
- a **plugin execution engine**
    
- a **dependency manager**
    

Maven itself does **very little work directly**.  
Most real work is performed by **plugins**.

---

# 2. The Three Core Components of Maven

Maven's architecture can be understood with three layers:

```
Lifecycle
   ↓
Phases
   ↓
Goals
```

Expanded version:

```
Maven Core
   ↓
Lifecycle
   ↓
Phases
   ↓
Goals (from plugins)
   ↓
Plugin implementation code
```

---

# 3. Lifecycle

A **lifecycle** is the overall build workflow.

Maven defines **three built-in lifecycles**:

|Lifecycle|Purpose|
|---|---|
|clean|remove build artifacts|
|default|build and deploy project|
|site|generate documentation|

The most important lifecycle is the **default lifecycle**.

---

# 4. Default Lifecycle Phases

The default lifecycle contains ordered phases:

```
validate
compile
test
package
verify
install
deploy
```

When a phase runs, **all previous phases run automatically**.

Example:

```
mvn package
```

executes:

```
validate → compile → test → package
```

---

# 5. Phases

A **phase** is simply a checkpoint in the lifecycle.

Important point:

> Phases themselves contain **no logic**.

They only act as **hooks where goals are executed**.

Example phases:

|Phase|Purpose|
|---|---|
|compile|compile source code|
|test|run tests|
|package|create distributable artifact|

---

# 6. Plugins

Plugins are where **actual work happens**.

Examples:

|Plugin|Responsibility|
|---|---|
|maven-compiler-plugin|compile Java|
|maven-surefire-plugin|run tests|
|maven-jar-plugin|create JAR|
|spring-boot-maven-plugin|build executable Spring Boot JAR|

Plugins are distributed as **normal Maven artifacts (JAR files)**.

---

# 7. Goals

A **goal** is a specific task provided by a plugin.

Example plugin:

```
maven-compiler-plugin
```

Goals:

```
compile
testCompile
```

A goal is executed using the syntax:

```
pluginPrefix:goal
```

Example:

```
compiler:compile
```

Structure:

```
pluginPrefix : goal
```

---

# 8. What Actually Executes During a Build

When you run:

```
mvn package
```

Maven:

1. determines phases to run
    
2. collects goals bound to each phase
    
3. executes those goals
    

Example execution flow:

```
compile phase → compiler:compile
test phase → surefire:test
package phase → jar:jar
```

In Spring Boot projects:

```
package phase →
   jar:jar
   spring-boot:repackage
```

---

# 9. Who Defines What (The Actors)

Understanding Maven requires recognizing **four actors**.

### 1️⃣ Maven Core

Defines:

- lifecycle
    
- phases
    
- execution engine
    

Example phases:

```
compile
test
package
```

---

### 2️⃣ Plugins

Plugins define:

- goals
    
- implementation logic
    

Example:

```
maven-compiler-plugin
   goal: compile
```

The plugin contains Java code that runs the compiler.

---

### 3️⃣ Default Bindings (Maven)

Maven automatically binds certain goals to phases depending on **packaging type**.

Example for `jar` packaging:

```
compile phase → compiler:compile
test phase → surefire:test
package phase → jar:jar
```

These bindings are built into Maven.

---

### 4️⃣ User Configuration (POM)

The user can add new bindings.

Example:

```xml
<plugin>
  <artifactId>maven-shade-plugin</artifactId>

  <executions>
    <execution>
      <phase>package</phase>
      <goals>
        <goal>shade</goal>
      </goals>
    </execution>
  </executions>

</plugin>
```

Now during `package` Maven runs:

```
jar:jar
shade:shade
```

---

# 10. Binding Goals to Phases

A **binding** connects:

```
phase → goal
```

Example binding:

```
package → jar:jar
```

Custom binding example:

```
package → my-plugin:explode
```

Multiple goals may run in the same phase.

Example:

```
package phase →
   jar:jar
   spring-boot:repackage
   shade:shade
```

---

# 11. Running Phases vs Goals

## Running a Phase

```
mvn package
```

This triggers the lifecycle.

Execution:

```
validate
compile
test
package
```

Goals bound to each phase run automatically.

---

## Running a Goal Directly

Syntax:

```
mvn pluginPrefix:goal
```

Example:

```
mvn dependency:tree
```

This executes only that goal.

---

# 12. Plugin Prefix Resolution

When Maven sees:

```
compiler:compile
```

it resolves:

```
compiler → maven-compiler-plugin
```

So the full plugin is:

```
org.apache.maven.plugins:maven-compiler-plugin
```

---

# 13. Where Goal Logic Lives

Goals are implemented as Java classes called **Mojos**.

Mojo = **Maven Old Java Object**

Example:

```
CompilerMojo
```

This class implements the `compile` goal.

Plugin JAR contains metadata:

```
META-INF/maven/plugin.xml
```

This file declares:

```
available goals
goal parameters
default bindings
```

---

# 14. Maven Properties (`-D`)

CLI parameters allow passing build properties.

Example:

```
mvn package -DskipTests
```

This creates property:

```
skipTests=true
```

Plugins or the POM may read it:

```
${skipTests}
```

Important:

> Maven itself does not interpret most properties.  
> Plugins decide how to use them.

---

# 15. Build-Time vs Runtime Configuration

Important separation:

### Build-time (Maven)

Controls how software is built.

Examples:

```
skip tests
java version
plugin settings
artifact name
```

Configured via:

```
pom.xml
-D properties
```

---

### Runtime (Application)

Controls application behavior.

Examples:

```
database URL
API keys
server port
logging level
```

Configured via:

```
application.properties
environment variables
```

---

# 16. Multi-Module Projects

Typical structure:

```
project-root
│
├── pom.xml
├── module-a
├── module-b
└── module-c
```

Root POM defines:

```
<modules>
```

Maven builds modules in dependency order.

---

# 17. Key Mental Model

A complete Maven execution looks like:

```
mvn package
   ↓
Maven runs lifecycle
   ↓
phases are executed
   ↓
goals bound to phases are collected
   ↓
plugin goals run
   ↓
plugin code performs the work
```

---

# 18. Final Conceptual Model

```
Maven Core
   ↓
Lifecycle
   ↓
Phases
   ↓
Goals
   ↓
Plugins (implementation)
```

Responsibilities:

|Responsibility|Owner|
|---|---|
|Lifecycle structure|Maven|
|Task implementation|Plugins|
|Goal scheduling|Default bindings + user configuration|

---

# Key Takeaways

1. **Phases are fixed** by Maven.
    
2. **Goals are defined by plugins.**
    
3. **Goals are bound to phases.**
    
4. **Running a phase triggers all goals bound to it.**
    
5. **Plugins contain the real build logic.**
    

---


# Maven — Complete Deep Notes (Part 1)

## 1. What Maven Actually Is

At its core, **Maven is three things simultaneously**:

1️⃣ **Build Tool**  
Compiles code, runs tests, packages artifacts.

2️⃣ **Dependency Manager**  
Downloads and manages libraries automatically.

3️⃣ **Project Standardizer**  
Defines a **standard structure and lifecycle**.

Without Maven, building Java apps used to be chaos.

Old workflow:

```
javac *.java
jar cf app.jar *.class
java -cp lib/*
```

Every project had **custom scripts**.

Maven standardized everything.

---

# 2. Maven Philosophy

Maven has a core design principle:

## Convention Over Configuration

If you follow Maven conventions, you barely configure anything.

Example:

```
src/main/java
src/test/java
src/main/resources
```

Because of this convention:

```
mvn compile
```

automatically knows:

- where the code is
    
- where to output compiled classes
    
- where resources are
    
- where tests are
    

---

# 3. Maven Project Structure

Standard Maven layout:

```
project
│
├── pom.xml
│
├── src
│   ├── main
│   │   ├── java
│   │   ├── resources
│   │
│   └── test
│       ├── java
│       └── resources
│
├── target
```

Explanation:

|Folder|Purpose|
|---|---|
|pom.xml|Project configuration|
|src/main/java|Application source|
|src/main/resources|configs, properties|
|src/test/java|unit tests|
|target|compiled artifacts|

The **target directory is generated by Maven**.

Example:

```
target/
   classes/
   test-classes/
   myapp.jar
```

Never commit `target`.

---

# 4. The POM File (Project Object Model)

The **pom.xml** is Maven’s brain.

Example minimal POM:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myapp</artifactId>
    <version>1.0</version>

</project>
```

These three identifiers uniquely define your artifact.

```
groupId: organization
artifactId: project name
version: release version
```

Together they create:

```
com.example:myapp:1.0
```

This is how Maven identifies artifacts.

---

# 5. Artifact Coordinates

Every library in Maven is identified using:

```
groupId
artifactId
version
```

Example:

```
org.springframework:spring-core:6.0.3
```

This is called **GAV coordinates**.

Maven downloads artifacts using these coordinates.

---

# 6. Maven Repositories

Where dependencies are stored.

Three levels:

## Local Repository

Location:

```
~/.m2/repository
```

First place Maven checks.

Example:

```
~/.m2/repository/org/springframework/spring-core
```

---

## Central Repository

Default public repo:

```
https://repo.maven.apache.org/maven2
```

If dependency not found locally → downloaded from here.

---

## Remote Repository

Organizations often host their own.

Example:

```
Nexus
Artifactory
GitHub packages
AWS CodeArtifact
```

Configured in pom.xml.

---

# 7. Dependency Management

Example dependency:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>6.0.3</version>
    </dependency>
</dependencies>
```

When you run:

```
mvn compile
```

Maven:

1️⃣ Checks local repo  
2️⃣ If missing → downloads dependency  
3️⃣ Adds it to classpath

---

# 8. Transitive Dependencies

One of Maven’s **most powerful features**.

Example:

You add:

```
spring-boot-starter-web
```

But that dependency itself depends on:

```
spring-core
spring-context
spring-web
jackson
tomcat
```

Maven automatically pulls all of them.

Graph example:

```
A
├── B
│   └── D
└── C
```

You add **A**, Maven downloads **B, C, D**.

---

# 9. Dependency Scope

Scopes define **when dependencies are used**.

|Scope|Available in|
|---|---|
|compile|everywhere|
|provided|compile but not runtime|
|runtime|runtime only|
|test|tests only|

Example:

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <scope>test</scope>
</dependency>
```

JUnit only available for tests.

---

# 10. Maven Lifecycle

This is where Maven becomes **really powerful**.

Maven defines a **standard build lifecycle**.

The main lifecycle:

```
validate
compile
test
package
verify
install
deploy
```

Running:

```
mvn package
```

executes:

```
validate
compile
test
package
```

All earlier phases run automatically.

---

# 11. Lifecycle Phases Explained

### validate

Check project correctness.

### compile

Compile source code.

```
src/main/java → target/classes
```

---

### test

Runs unit tests.

Test framework:

```
Surefire plugin
```

---

### package

Creates artifact.

Example:

```
jar
war
```

---

### verify

Integration tests, checks.

---

### install

Copies artifact to **local Maven repo**.

```
~/.m2/repository
```

Other projects on machine can use it.

---

### deploy

Uploads artifact to **remote repository**.

Used in CI/CD pipelines.

---

# 12. Maven Plugins

Maven itself is small.

Most functionality comes from **plugins**.

Example:

```
compile → maven-compiler-plugin
test → maven-surefire-plugin
package → maven-jar-plugin
```

Plugins extend Maven.

---

Example plugin configuration:

```xml
<build>
  <plugins>

    <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.10.1</version>
        <configuration>
            <source>17</source>
            <target>17</target>
        </configuration>
    </plugin>

  </plugins>
</build>
```

This configures Java version.

---

# 13. Build Lifecycle vs Plugins

Important relationship.

Lifecycle:

```
compile
```

Plugin goal:

```
compiler:compile
```

Lifecycle phases **trigger plugin goals**.

Example:

```
compile phase → compiler:compile goal
```

---

# 14. Maven Goals

Goal = specific task executed by plugin.

Example:

```
mvn compiler:compile
```

But usually we run **phases instead of goals**.

Example:

```
mvn compile
```

Which internally runs the goal.

---

# 15. Packaging Types

Packaging defines **what artifact Maven produces**.

Common types:

|Type|Output|
|---|---|
|jar|Java library|
|war|web application|
|ear|enterprise archive|
|pom|parent project|

Example:

```xml
<packaging>jar</packaging>
```

---

# 16. Clean Lifecycle

Separate lifecycle:

```
clean
```

Command:

```
mvn clean
```

Deletes:

```
target/
```

So builds start fresh.

---

# Where we'll continue next (Part 2)

Next topics (the **important Maven internals**):

1️⃣ Dependency conflict resolution  
2️⃣ Dependency tree analysis  
3️⃣ Exclusions  
4️⃣ Dependency management vs dependencies  
5️⃣ Parent POM  
6️⃣ Multi-module projects  
7️⃣ Profiles  
8️⃣ Maven build order  
9️⃣ Plugin lifecycle bindings  
10️⃣ Effective POM  
11️⃣ Maven settings.xml  
12️⃣ Corporate repositories (Nexus/Artifactory)  
13️⃣ CI/CD with Maven  
14️⃣ Maven vs Gradle architecture

---

---

# Maven Deep Notes — Part 2

## Dependency Resolution, Conflict Hell, Parent POMs, Multi-Module Builds

---

# 1. Dependency Tree (The Reality Behind Maven)

When you add one dependency, Maven actually builds a **dependency graph**.

Example:

You add:

```xml
spring-boot-starter-web
```

Real tree becomes something like:

```
spring-boot-starter-web
├── spring-web
│   ├── spring-core
│   └── jackson-databind
├── spring-boot
│   └── spring-context
└── tomcat-embed-core
```

This is why sometimes you suddenly get **30 libraries**.

To inspect this:

```
mvn dependency:tree
```

Output example:

```
com.example:app
 └─ spring-boot-starter-web
     ├─ spring-web
     ├─ spring-core
     └─ jackson-databind
```

This command is **mandatory for debugging dependency issues**.

---

# 2. Dependency Conflict (The Famous Maven Hell)

Suppose two dependencies require different versions.

Example:

```
A → B → C (version 1.0)
A → D → C (version 2.0)
```

Graph:

```
A
├── B
│   └── C 1.0
└── D
    └── C 2.0
```

Which version wins?

Maven rule:

### "Nearest Definition Wins"

Example:

```
A
├── C 1.0
└── B
    └── C 2.0
```

Winner:

```
C 1.0
```

Because it is **closer to root**.

---

### Second rule

If same depth:

### "First Declaration Wins"

Example:

```
dependencies:
    A
    B
```

If both pull different C versions:

The dependency declared **first** wins.

This is extremely important in large projects.

---

# 3. Dependency Exclusion

Sometimes you want to remove a transitive dependency.

Example:

```
spring-boot-starter-web
 └── jackson
```

But maybe you want another version.

You exclude it.

Example:

```xml
<dependency>
  <groupId>spring</groupId>
  <artifactId>starter</artifactId>

  <exclusions>
      <exclusion>
          <groupId>com.fasterxml.jackson</groupId>
          <artifactId>jackson-databind</artifactId>
      </exclusion>
  </exclusions>

</dependency>
```

Now Maven **will not pull jackson automatically**.

You can define your own version.

---

# 4. Dependency Management vs Dependencies

This confuses almost everyone.

Two sections:

```
dependencies
dependencyManagement
```

### dependencies

Actually adds dependency.

Example:

```xml
<dependencies>
  <dependency>
     <groupId>spring</groupId>
     <artifactId>spring-core</artifactId>
  </dependency>
</dependencies>
```

---

### dependencyManagement

Only defines **version control**.

Example:

```xml
<dependencyManagement>
  <dependencies>

     <dependency>
        <groupId>spring</groupId>
        <artifactId>spring-core</artifactId>
        <version>6.0</version>
     </dependency>

  </dependencies>
</dependencyManagement>
```

This **does NOT add dependency**.

It only says:

> If someone uses spring-core, use version 6.0.

---

# 5. Parent POM

Parent POM allows **shared configuration across projects**.

Example structure:

```
company-parent
│
├── service-a
├── service-b
└── service-c
```

Parent POM:

```
company-parent/pom.xml
```

Child POM:

```
service-a/pom.xml
```

Child references parent:

```xml
<parent>
   <groupId>com.company</groupId>
   <artifactId>company-parent</artifactId>
   <version>1.0</version>
</parent>
```

Now child inherits:

- dependency versions
    
- plugin config
    
- repositories
    
- properties
    

---

# 6. Effective POM

Maven merges multiple POMs.

Sources include:

```
child POM
parent POM
super POM
```

Final result:

```
Effective POM
```

To inspect:

```
mvn help:effective-pom
```

This shows the **actual build configuration Maven uses**.

Very useful for debugging.

---

# 7. The Super POM

Maven internally defines a hidden **super POM**.

It includes default configs like:

```
central repository
plugin versions
build directories
```

All projects inherit it automatically.

---

# 8. Multi-Module Maven Projects

Very common in large systems.

Structure:

```
company-project
│
├── pom.xml
├── api
├── service
└── repository
```

Root pom:

```xml
<packaging>pom</packaging>

<modules>
   <module>api</module>
   <module>service</module>
   <module>repository</module>
</modules>
```

Now building root:

```
mvn clean install
```

Builds all modules.

---

# 9. Module Dependency Example

```
api
service
repository
```

service depends on api.

```
service
 └── api
```

Maven automatically builds in correct order.

Build order becomes:

```
api
repository
service
```

Maven determines order using dependency graph.

---

# 10. Maven Profiles

Profiles allow **environment-specific builds**.

Example environments:

```
dev
qa
prod
```

Example:

```xml
<profiles>

  <profile>
     <id>dev</id>

     <properties>
        <db.url>localhost</db.url>
     </properties>

  </profile>

</profiles>
```

Activate:

```
mvn package -Pdev
```

---

# 11. Properties

Reusable variables.

Example:

```xml
<properties>
   <java.version>17</java.version>
</properties>
```

Used like:

```xml
<source>${java.version}</source>
```

Spring Boot uses this heavily.

---

# 12. settings.xml

Global Maven configuration.

Location:

```
~/.m2/settings.xml
```

Used for:

- credentials
    
- mirrors
    
- private repositories
    
- proxies
    

Example:

```xml
<servers>
   <server>
      <id>company-repo</id>
      <username>user</username>
      <password>pass</password>
   </server>
</servers>
```

---

# 13. Corporate Artifact Repositories

Companies rarely download directly from Maven Central.

Instead they use:

```
Nexus
Artifactory
AWS CodeArtifact
```

Architecture:

```
Developer
   ↓
Corporate Repo
   ↓
Maven Central
```

Benefits:

- security
    
- caching
    
- version control
    
- internal libraries
    

---

# 14. CI/CD Maven Flow

Typical pipeline:

```
Git push
   ↓
Jenkins
   ↓
mvn clean package
   ↓
run tests
   ↓
build docker image
   ↓
push to registry
   ↓
deploy
```

Command used in CI most often:

```
mvn clean install -DskipTests
```

or

```
mvn clean verify
```

---

# 15. Maven vs Gradle Architecture

Maven:

```
XML configuration
Lifecycle driven
Convention based
```

Gradle:

```
Code based (Groovy/Kotlin)
Task graph
More flexible
```

Example Maven:

```xml
<dependency>
```

Gradle:

```
implementation("spring-core")
```

Gradle is faster because:

```
incremental builds
daemon
better caching
```

But Maven is **more predictable**.

---

# Next (Part 3 — Advanced Maven Internals)

Next topics we can go even deeper:

1️⃣ Maven plugin architecture  
2️⃣ Writing custom Maven plugins  
3️⃣ Build lifecycle bindings  
4️⃣ How Maven resolves dependencies internally  
5️⃣ Reactor build system  
6️⃣ SNAPSHOT vs release versions  
7️⃣ Maven version ranges  
8️⃣ BOM (Bill of Materials)  
9️⃣ Spring Boot parent POM architecture  
10️⃣ Performance tuning Maven builds  
11️⃣ Parallel builds  
12️⃣ Debugging Maven builds  
13️⃣ Maven wrapper  
14️⃣ Real-world enterprise Maven structure


---

# Maven Deep Notes — Part 3

## Maven Internals, Plugins, SNAPSHOTs, BOMs, Reactor Builds, Enterprise Patterns

---

# 1. Maven Plugin Architecture

Maven itself is **extremely small**.  
Almost everything is implemented as a **plugin**.

Example phases:

```
compile
test
package
```

are actually implemented by plugins like:

|Phase|Plugin|
|---|---|
|compile|maven-compiler-plugin|
|test|maven-surefire-plugin|
|package|maven-jar-plugin|

So when you run:

```
mvn compile
```

Maven internally runs something like:

```
compiler:compile
```

Where:

```
plugin:goal
```

Example:

```
compiler:compile
surefire:test
jar:jar
```

This design makes Maven **extensible**.

---

# 2. Plugin Goals

Plugins contain **goals**.

Example plugin:

```
maven-compiler-plugin
```

Goals:

```
compile
testCompile
```

Example plugin:

```
maven-clean-plugin
```

Goal:

```
clean
```

You can run them directly:

```
mvn compiler:compile
```

But normally we run **phases**, not goals.

---

# 3. Lifecycle Bindings

Lifecycle phases are mapped to plugin goals.

Example mapping:

|Lifecycle Phase|Plugin Goal|
|---|---|
|compile|compiler:compile|
|test|surefire:test|
|package|jar:jar|

So:

```
mvn package
```

runs:

```
compile
test
package
```

which triggers:

```
compiler:compile
surefire:test
jar:jar
```

---

# 4. Maven Reactor (Multi-module build engine)

The **Reactor** is Maven’s system for building multi-module projects.

Example structure:

```
company-system
│
├── pom.xml
├── auth-service
├── payment-service
├── user-service
```

Root POM:

```xml
<modules>
   <module>auth-service</module>
   <module>payment-service</module>
   <module>user-service</module>
</modules>
```

When you run:

```
mvn install
```

The Reactor:

1️⃣ Reads all module POMs  
2️⃣ Builds dependency graph  
3️⃣ Determines build order

Example dependency:

```
user-service → auth-service
```

Build order becomes:

```
auth-service
user-service
payment-service
```

Maven **auto-detects build order**.

---

# 5. Parallel Builds

For large projects:

```
mvn -T 4 clean install
```

`-T` = threads.

Example:

```
mvn -T 1C clean install
```

Meaning:

```
1 thread per CPU core
```

This massively speeds up builds in big monorepos.

---

# 6. SNAPSHOT Versions

Very important concept.

Example version:

```
1.0-SNAPSHOT
```

Meaning:

```
development version
```

Snapshots are **mutable**.

Example:

```
1.0-SNAPSHOT
```

can be redeployed multiple times.

Release versions cannot.

Example release:

```
1.0
```

Once deployed → immutable.

---

# 7. How SNAPSHOT Resolution Works

Suppose dependency:

```
1.0-SNAPSHOT
```

Maven checks:

```
local repo
```

If update policy allows → Maven checks remote repo.

Snapshot actually stored as:

```
1.0-20260309.123456-1
```

Timestamped builds.

This allows Maven to fetch latest snapshot.

---

# 8. Release Versions

Release versions:

```
1.0
1.1
2.0
```

Rules:

```
immutable
never overwritten
```

Companies enforce this strongly.

Example:

```
Nexus
Artifactory
```

prevent redeploying same version.

---

# 9. Version Ranges (Rarely Used but Important)

Example:

```
[1.0,2.0)
```

Meaning:

```
>=1.0 and <2.0
```

Other examples:

```
[1.0]
(1.0,2.0)
[1.0,)
```

But version ranges are discouraged because:

```
non-reproducible builds
```

Most companies pin exact versions.

---

# 10. BOM (Bill of Materials)

Very important in large frameworks.

Example:

```
Spring Boot
```

Spring Boot provides a **BOM**.

Example:

```xml
<dependencyManagement>

<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-dependencies</artifactId>
   <version>3.2.0</version>
   <type>pom</type>
   <scope>import</scope>
</dependency>

</dependencyManagement>
```

This defines versions for **hundreds of dependencies**.

Now you can write:

```xml
<dependency>
   <groupId>org.springframework</groupId>
   <artifactId>spring-core</artifactId>
</dependency>
```

No version required.

The BOM supplies it.

---

# 11. Why BOMs Exist

Without BOM:

```
spring-core 6.0
spring-web 5.2
spring-context 6.0
```

Version mismatch = runtime errors.

BOM ensures **compatible versions**.

---

# 12. Spring Boot Parent POM

Spring Boot apps usually inherit:

```xml
<parent>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-parent</artifactId>
</parent>
```

This parent provides:

- dependency management
    
- plugin versions
    
- Java defaults
    
- encoding settings
    

This is why Spring Boot POMs look tiny.

---

# 13. Enterprise Maven Structure

Large companies usually structure Maven like:

```
company-parent
│
├── company-bom
├── shared-libraries
│   ├── auth-lib
│   ├── logging-lib
│   └── metrics-lib
│
└── services
    ├── user-service
    ├── order-service
    └── payment-service
```

Parent POM defines:

```
Java version
plugin versions
repositories
dependency management
```

Services inherit parent.

---

# 14. Maven Wrapper

Instead of requiring Maven installed:

Projects include:

```
mvnw
mvnw.cmd
.mvn/
```

Run:

```
./mvnw clean install
```

Wrapper downloads correct Maven version automatically.

Very common in modern repos.

---

# 15. Debugging Maven Builds

Useful commands:

### Dependency tree

```
mvn dependency:tree
```

### Effective POM

```
mvn help:effective-pom
```

### Plugin help

```
mvn help:describe -Dplugin=compiler
```

### Debug mode

```
mvn -X clean install
```

### Offline mode

```
mvn -o package
```

---

# 16. Skipping Tests

Common commands:

Skip running tests:

```
mvn package -DskipTests
```

Skip compiling tests:

```
mvn package -Dmaven.test.skip=true
```

Difference:

|Flag|Behaviour|
|---|---|
|skipTests|compile tests but don't run|
|maven.test.skip|skip entirely|

---

# 17. Maven Performance Tips

Speed up builds:

```
mvn -T 1C clean install
```

Skip tests:

```
-DskipTests
```

Use local repo caching.

CI servers often cache:

```
~/.m2/repository
```

---

# 18. Maven vs Gradle (Engineering View)

Maven strengths:

```
predictable
standardized
stable
```

Gradle strengths:

```
faster
more flexible
better incremental builds
```

Large enterprises still heavily use Maven because:

```
predictability > flexibility
```

---


---

# How Maven Actually Builds a Spring Boot Project (Step-by-Step)

We’ll walk through **what literally happens inside the filesystem and JVM** when you run Maven.

Assume project structure:

```
myapp
│
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com/example/Application.java
│   │   └── resources
│   │       └── application.properties
│   │
│   └── test
│       └── java
│           └── ApplicationTest.java
```

---

# The Maven Build Lifecycle (What It Actually Does)

When you run:

```
mvn package
```

Maven runs a **sequence of phases**.

```
validate
compile
test
package
```

Each phase **transforms the project in some way**.

Think of it as a **pipeline**.

```
source code
   ↓
compiled classes
   ↓
tested classes
   ↓
packaged artifact
```

---

# Phase 1 — validate

Goal: **Check the project is buildable**

What Maven checks:

- `pom.xml` syntax
    
- dependencies defined correctly
    
- plugins available
    
- project structure valid
    

Example failure:

```
dependency version missing
invalid XML
plugin not found
```

Nothing is compiled yet.

Filesystem change:

```
No files created
```

---

# Phase 2 — compile

Goal: **Convert Java source → bytecode**

Input:

```
src/main/java
```

Example file:

```
Application.java
```

Java source:

```java
public class Application {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

Maven invokes the **Java compiler**:

```
javac
```

Command roughly equivalent to:

```
javac -d target/classes src/main/java/**/*.java
```

Output directory:

```
target/classes
```

Result:

```
target
└── classes
    └── com/example/Application.class
```

The `.class` file is **JVM bytecode**.

This is the first real transformation.

---

# Phase 3 — test-compile

Now Maven compiles **test code**.

Input:

```
src/test/java
```

Output:

```
target/test-classes
```

Example:

```
ApplicationTest.class
```

Tests compile **against main classes**.

Classpath during test compilation:

```
target/classes
dependencies
test dependencies
```

---

# Phase 4 — test

Goal: **Run unit tests**

Maven invokes plugin:

```
maven-surefire-plugin
```

Surefire:

1. Finds test classes
    
2. Starts JVM
    
3. Executes tests
    

Example:

```
ApplicationTest
```

Test framework:

```
JUnit
TestNG
```

Results stored in:

```
target/surefire-reports
```

Example report:

```
TEST-com.example.ApplicationTest.xml
```

Console output example:

```
Tests run: 5, Failures: 0
```

If tests fail:

```
BUILD FAILURE
```

Build stops here.

---

# Phase 5 — package

Goal: **Create distributable artifact**

For Spring Boot:

```
jar
```

Normal Java jar:

```
target/myapp.jar
```

But Spring Boot uses **fat jars**.

Meaning the jar contains:

```
your classes
dependencies
embedded server
```

Inside jar:

```
myapp.jar
│
├── BOOT-INF
│   ├── classes
│   └── lib
│
└── META-INF
```

Example:

```
BOOT-INF/classes
BOOT-INF/lib
```

Dependencies stored inside:

```
BOOT-INF/lib/spring-core.jar
BOOT-INF/lib/jackson.jar
```

This allows:

```
java -jar myapp.jar
```

to run **without external libraries**.

---

# Phase 6 — install

Goal:

```
publish artifact to local Maven repository
```

Location:

```
~/.m2/repository
```

Example path:

```
~/.m2/repository/com/example/myapp/1.0/myapp-1.0.jar
```

Now **other local projects can depend on it**.

Example dependency:

```xml
<dependency>
  <groupId>com.example</groupId>
  <artifactId>myapp</artifactId>
  <version>1.0</version>
</dependency>
```

---

# Phase 7 — deploy

Goal:

```
upload artifact to remote repository
```

Example:

```
Nexus
Artifactory
AWS CodeArtifact
```

Now other developers can download it.

---

# What Makes Spring Boot Different

Spring Boot adds plugin:

```
spring-boot-maven-plugin
```

This plugin **modifies the package phase**.

Normally Maven produces:

```
simple jar
```

Spring Boot produces:

```
executable jar
```

Plugin goal:

```
spring-boot:repackage
```

What it does:

1. Takes compiled classes
    
2. Bundles dependencies
    
3. Adds boot loader
    
4. Creates runnable jar
    

---

# What Happens When You Run

```
java -jar myapp.jar
```

The boot loader inside jar:

```
org.springframework.boot.loader
```

does this:

1. Extracts classpath
    
2. Loads dependencies
    
3. Starts embedded server
    
4. Runs main method
    

This is why Spring Boot apps **run like executables**.

---

# Complete Build Transformation

```
src/main/java
   ↓
compile
   ↓
target/classes
   ↓
test
   ↓
target/test-reports
   ↓
package
   ↓
target/myapp.jar
   ↓
install
   ↓
~/.m2/repository
```

---

# Typical Spring Boot Build Commands

Clean build:

```
mvn clean package
```

Run app directly:

```
mvn spring-boot:run
```

Skip tests:

```
mvn package -DskipTests
```

---

# What `clean` Actually Does

Deletes:

```
target/
```

This forces **full rebuild**.

Equivalent to:

```
rm -rf target
```

---

# If you're studying Maven seriously, the next important topics are

1. **How Spring Boot fat jars actually work internally**
    
2. **Classpath construction during Maven builds**
    
3. **Why dependency conflicts happen**
    
4. **How Maven resolves version conflicts**
    
5. **How the JVM loads dependencies from fat jars**
    

These are the things that make Maven finally **click at a deep level**.

---

