How Maven, Java, and Spring Boot See Your Project

---

## 🧱 1. Maven – The **Build System**

### What it sees:

- Maven organizes your project into **modules**.
    
- It controls:
    
    - Build order
        
    - Dependency resolution
        
    - Packaging (JAR/WAR)
        
    - Classpath setup for each module
        

### How modules work:

- Module A can depend on Module B via:
    
    ```xml
    <dependency>
      <groupId>com.example</groupId>
      <artifactId>core</artifactId>
    </dependency>
    ```
    
- Maven builds Module B first and adds its compiled `.class` files (and its dependencies) to Module A’s **classpath**.
    

👉 **Modules mean nothing to Java or Spring at runtime — they only exist at build time.**

---

## ☕ 2. Java – The **Language Runtime**

### What it sees:

- Java sees **classes on the classpath**, grouped by their **fully qualified package names**.
    
- Java doesn’t know or care whether those classes came from:
    
    - Different modules
        
    - JARs
        
    - Compiled `.class` files
        

### Package behavior:

- Two classes in **different modules** but **same package** are seen as if they’re in the same "namespace" (they _are_ in Java terms).
    
- This can lead to:
    
    - **Field/method visibility leakage**
        
    - **`protected`/package-private access working across modules**
        

📌 **Important:** Java sees **packages**, not modules.

---

## 🌱 3. Spring Boot – The **Runtime Framework**

### What it sees:

- Spring uses:
    
    - The **classpath** (from Maven)
        
    - **Package scanning**, starting from `@SpringBootApplication`
        
    - Annotation-based metadata (`@Component`, `@Service`, `@Configuration`, etc.)
        

### Component scanning logic:

By default:

```java
@SpringBootApplication
public class MyApp {}
```

➡️ Spring **auto-scans all packages below** the one containing `MyApp`.

If `MyApp` is in `com.example.app`, it scans:

- `com.example.app.service`
    
- `com.example.app.config`
    
- **But not** `com.example.core.config` unless it’s below `com.example.app`
    

### Exception: You can **manually include packages** via:

```java
@SpringBootApplication(scanBasePackages = {
    "com.example.app",
    "com.example.core"
})
```

### Why your `SecurityConfig` got picked up:

- Even though it's in **another Maven module**, it was in the **same package name** (`com.example.config`) as the main app.
    
- Spring scanned the package, found it on the classpath (thanks to Maven), and registered it.
    

✅ This is valid and expected behavior — **Spring doesn’t care about Maven module boundaries**, only packages and annotations.

---

## ⚠️ Gotcha: Same Package Across Modules

If both modules (say `core` and `profile`) have classes in the same package like:

```
com.example.security
```

- Java will treat them as **one package**
    
- Spring will happily scan and load all components
    
- But you could get **class conflicts**, **strange behavior**, or **hidden bugs** if you’re not careful
    

✅ It’s safer to keep **module-specific code in module-specific packages**, like:

```
com.example.core.security
com.example.profile.security
```

---

## 🧠 TL;DR Cheat Sheet

|Tool|Sees|Cares About Maven Modules?|How It Groups Things|
|---|---|---|---|
|**Maven**|Modules, Dependencies|✅ YES|Module hierarchy in `pom.xml`|
|**Java**|Packages, Classpath|❌ NO|Groups by `package com.example.*`|
|**Spring Boot**|Packages via classpath|❌ NO|Scans based on `@SpringBootApplication` package and annotations|

---

## 🧼 Best Practices

1. **Avoid using the same package name across modules** — especially if both declare classes in the same package (`com.example.config`)
    
2. **Put `@SpringBootApplication` in the root-most package** that should be scanned.
    
3. **Use `@Import(...)`** if you want to be explicit about pulling in configs from other modules.
    
4. **Use module-specific base packages**, like:
    
    ```
    com.myapp.core.*
    com.myapp.profile.*
    ```
    
    Even if they're logically related.
    
5. **Think of Maven modules like JARs** — they’re just chunks of compiled classes. Nothing magical about their structure at runtime.


Packaging

---

### Common Maven packaging types

1. **`jar`**
    
    - Default if you don’t specify anything.
        
    - Produces a `.jar` file (plain Java library).
        
    - Used for reusable libraries or modules that don’t need to be run standalone.
        
    - Example: your `bike-domain` module → builds `bike-domain-0.0.1-SNAPSHOT.jar`.
        

---

2. **`war`**
    
    - Produces a `.war` (Web Application Archive).
        
    - Old-school Java EE / Servlet container deployments (Tomcat, JBoss).
        
    - With Spring Boot, you normally don’t need WAR (since Boot runs as a fat JAR), unless you must deploy into an external app server.
        

---

3. **`ear`**
    
    - Enterprise Archive (for full-blown Java EE apps with EJBs, WARs, etc.).
        
    - Rarely used today outside legacy enterprise stacks.
        

---

4. **`pom`**
    
    - Special type: produces no artifact (no JAR/WAR).
        
    - Instead, it’s a **container for other modules** (a “parent” or “aggregator”).
        
    - Lets you group projects together, define shared dependencies, pluginManagement, etc.
        
    - That’s what your **parent project** will be.
        

---

5. **Others / plugins provide custom types**
    
    - e.g. `maven-plugin` → for writing Maven plugins.
        
    - Some frameworks add custom packaging, but the big 4 are `jar`, `war`, `ear`, and `pom`.
        

---

### Why `packaging=pom` for parent?

Because the parent project isn’t meant to produce an app or library. It just:

- Collects submodules (`<modules>`).
    
- Defines shared dependency versions (`<dependencyManagement>`).
    
- Defines shared plugin versions (`<pluginManagement>`).
    

Maven won’t try to compile classes or produce a JAR/WAR in the parent, since there’s nothing to build — just coordination.

---

⚡ TL;DR:

- `jar` = library
    
- `war` = web app for servlet container
    
- `ear` = enterprise app (rare today)
    
- `pom` = aggregator/parent
    

---

# 📌 Spring Boot Dependencies (BOM) — Quick Notes

### What is it?

- **`spring-boot-dependencies`** is a **Bill of Materials (BOM)** POM file.
    
- It defines the **versions** of hundreds of libraries that are compatible with a specific Spring Boot release (e.g. 3.3.4).
    
- You import it once (in parent POM), and child modules automatically get the right versions.
    

---

### Why do we need it?

- Spring Boot pulls in many libraries: Spring Framework, Hibernate, Jackson, Tomcat/Jetty, Logback, Micrometer, etc.
    
- Manually managing versions would be painful and error-prone.
    
- BOM ensures **version alignment** and **compatibility**.
    

---

### How to use it?

In **parent POM**:

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>${spring.boot.version}</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

Then in **child modules**, declare dependencies **without versions**:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Maven will resolve the version from the BOM.

---

### What it includes

- Versions for **Spring projects** (Spring Core, Data, Security, etc.).
    
- Common **3rd-party libs** (Hibernate, Jackson, Logback, Tomcat, Micrometer, etc.).
    
- Test libs (JUnit, AssertJ, Mockito).
    

Basically **everything needed for a Boot app**.

---

### What it does **not** do

- It doesn’t automatically add dependencies to your project.
    
- You still must declare which starters/libs you want — but no version needed.
    

---

### Analogy

- Parent BOM = 📖 “The official shopping list.”
    
- Child POM = 🛒 “I need milk and bread.”
    
- Maven = 🧑‍🍳 “I’ll get milk v3.1 and bread v2.0 because that’s what the shopping list says.”
    

---

✅ **Key Takeaway:**  
Always import `spring-boot-dependencies` in the parent POM of a multi-module Spring Boot project. This ensures consistent, compatible versions across all modules, saves you from “dependency hell,” and lets you omit versions in child modules.

---
