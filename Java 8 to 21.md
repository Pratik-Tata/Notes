**Java 8 to Java 21: In-Depth Feature & Change Guide**

---

# ✨ Java 8 (LTS) Recap - The Baseline (2014)

### Major Features:

- **Lambda Expressions**: `Runnable r = () -> System.out.println("Hi");`
    
- **Streams API**: Declarative collection processing
    
- **Default & Static Methods in Interfaces**n- **Optional**: Container object for nullable values
    
- **Date and Time API (java.time)**: `LocalDate`, `ZonedDateTime`
    
- **Nashorn JavaScript Engine**
    
- **Parallel Sort**: `Arrays.parallelSort()`
    
- **New Collectors**: `groupingBy`, `partitioningBy`
    

---

# 🕰 Java 9 (2017)

### Key Features:

- **Project Jigsaw (Module System)**: `module-info.java`
    
- **JShell (REPL)**: Run Java snippets interactively
    
- **Improved APIs**: `Optional.ifPresentOrElse()`, `Stream.takeWhile()`
    
- **Compact Strings in JVM**: Improves memory usage
    
- **Stack Walking API**
    
- **Private methods in interfaces**
    
- **`Flow` API (Reactive Streams standard)**
    

---

# 🕱 Java 10 (2018)

### Key Features:

- **Local Variable Type Inference**: `var name = "Java";`
    
- **Application Class-Data Sharing (AppCDS)**
    
- **G1 Garbage Collector Improvements**
    
- **Thread-Local Handshakes**
    
- **Root Certificates included in OpenJDK**
    

---

# 🕲 Java 11 (LTS - 2018)

### Major Features:

- **New String Methods**: `isBlank()`, `lines()`, `strip()`, `repeat()`
    
- **`var` in Lambda Params (Preview)**
    
- **HTTP Client API (Standard)**: `java.net.http.HttpClient`
    
- **Flight Recorder**: Built-in monitoring
    
- **Removed Deprecated APIs**: e.g., Java EE modules, CORBA, JavaFX from JDK
    
- **Launch Single-file Programs**: `java Hello.java`
    
- **ZGC (Experimental)**
    

---

# 🕳 Java 12 (2019)

### Highlights:

- **Switch Expressions (Preview)**: `switch (x) -> case A -> 1;`
    
- **Compact Number Formatting**
    
- **Shenandoah GC (Experimental)**
    
- **Microbenchmark Suite (jmh-like)**
    

---

# 🕴 Java 13 (2019)

### Highlights:

- **Text Blocks (Preview)**: `"""Multiline strings"""`
    
- **Dynamic CDS Archives**
    
- **Reimplement Legacy Socket API (NIO-based)**
    

---

# 🕵 Java 14 (2020)

### Highlights:

- **Records (Preview)**: Immutable data carriers `record Point(int x, int y)`
    
- **NullPointerExceptions with Detailed Messages**
    
- **Pattern Matching for instanceof (Preview)**
    
- **Helpful NPEs**: Better debugging info
    
- **NUMA-aware memory allocation**
    

---

# 🕶 Java 15 (2020)

### Highlights:

- **Text Blocks (Finalized)**
    
- **Sealed Classes (Preview)**: Restrict inheritance
    
- **Hidden Classes (for frameworks, runtime generation)**
    
- **ZGC support for Mac & Windows**
    
- **EdDSA Signature Algorithm Support**
    

---

# 🕷 Java 16 (2021)

### Highlights:

- **Records (Finalized)**
    
- **Pattern Matching for instanceof (2nd Preview)**
    
- **JEP 376: ZGC becomes production-ready**
    
- **JEP 394: Pattern Matching for instanceof**
    
- **Strong encapsulation of JDK internals by default**
    
- **Unix-Domain Socket Channels**
    

---

# 🕸 Java 17 (LTS - 2021)

### Major Additions:

- **Sealed Classes (Finalized)**
    
- **Pattern Matching (Finalized)**
    
- **New macOS Rendering Pipeline (Metal)**
    
- **Strong Encapsulation of JDK Internals**
    
- **Deprecation of Applet API**
    
- **Context-specific deserialization filters**
    
- **Foreign Function & Memory API (Incubator)**
    

---

# 🕹 Java 18 (2022)

### Highlights:

- **UTF-8 by Default**
    
- **Simple Web Server**: Great for testing
    
- **Code Snippets in JavaDocs**
    
- **Vector API (3rd Incubator)**
    
- **Internet-Address Resolution SPI**
    

---

# 🕺 Java 19 (2022)

### Major Features:

- **Virtual Threads (Preview)**: Lightweight threads via Project Loom
    
- **Structured Concurrency (Incubator)**
    
- **Pattern Matching for switch (Preview)**
    
- **Foreign Function & Memory API (Preview)**
    
- **Vector API (4th Incubator)**
    

---

# 🕻 Java 20 (2023)

### Features:

- **Record Patterns (Preview)**
    
- **Scoped Values (Incubator)**
    
- **Virtual Threads (2nd Preview)**
    
- **Structured Concurrency (2nd Incubator)**
    
- **Pattern Matching for switch (2nd Preview)**
    
- **Foreign Function & Memory API (2nd Preview)**
    

---

# 🕼 Java 21 (LTS - 2023)

### Massive Additions:

- **Virtual Threads (Finalized)**
    
- **Record Patterns (Preview)**
    
- **Pattern Matching for switch (Finalized)**
    
- **Sequenced Collections API**: `List.of().reversed()`
    
- **Unnamed Classes and Instance Main Methods (Preview)**
    
- **String Templates (Preview)**
    
- **Scoped Values (2nd Incubator)**
    
- **Foreign Function & Memory API (Preview)**
    

---

# 🔧 JVM & Runtime Changes

### Memory & Performance:

- **ZGC, Shenandoah GC**: Low-latency collectors
    
- **AppCDS Improvements**
    
- **Compact Strings**
    
- **JVM Constants API**
    
- **NUMA-aware Memory Allocation**
    
- **Improved Class Metadata Storage**
    

### Tooling & Debugging:

- **Flight Recorder**
    
- **JShell**
    
- **Helpful NullPointerExceptions**
    
- **JFR Event Streaming**
    
- **Code Snippets in JavaDocs**
    

### Security:

- Strong encapsulation (JEP 403)
    
- Deprecation/removal of unsafe/internal APIs
    
- Context-specific deserialization filters
    
- EdDSA Signature Algorithm Support
    
- Better cryptographic algorithm defaults





**Java + Jakarta Upgrade Cheat Sheet (2025 Edition)**

---

### 📊 Java Core Upgrades

|**Old API / Class**|**Modern Replacement**|**Since**|
|---|---|---|
|`java.net.HttpURLConnection`|`java.net.http.HttpClient`|Java 11|
|`java.util.Date`, `Calendar`|`java.time.*` (`LocalDate`, `Instant`, etc.)|Java 8|
|`StringBuffer`|`StringBuilder`|Java 5|
|`Vector`, `Hashtable`|`ArrayList`, `HashMap`|Java 1.2|
|`Enumeration`|`Iterator`, `Stream`|Java 1.2 / 8|
|`Future`|`CompletableFuture`|Java 8|
|`synchronized`|`Lock` (`java.util.concurrent.locks.Lock`)|Java 5|
|`wait()` / `notify()`|`java.util.concurrent` utilities|Java 5|
|Manual `Thread`|`ExecutorService`, `ForkJoinPool`, `VirtualThread`|Java 5/7/21|
|`System.gc()`|`java.lang.ref.Cleaner`|Java 9|

---

### 🏘️ Jakarta EE (formerly Java EE) Upgrades

|**Old `javax.*` Package**|**New `jakarta.*` Package**|**EE Version**|
|---|---|---|
|`javax.servlet.*`|`jakarta.servlet.*`|EE 9|
|`javax.persistence.*`|`jakarta.persistence.*`|EE 9|
|`javax.validation.*`|`jakarta.validation.*`|EE 9|
|`javax.ws.rs.*` (JAX-RS)|`jakarta.ws.rs.*`|EE 9|
|`javax.faces.*` (JSF)|`jakarta.faces.*`|EE 9|
|`javax.jms.*`|`jakarta.jms.*`|EE 9|
|`javax.mail.*`|`jakarta.mail.*`|EE 9|
|`javax.transaction.*`|`jakarta.transaction.*`|EE 9|
|`javax.json.*`|`jakarta.json.*`|EE 10+|

---

### 🔧 Modern Java Features Worth Knowing

|**Feature**|**API / Concept**|**Since**|
|---|---|---|
|Reactive Streams|`java.util.concurrent.Flow`|Java 9|
|Pattern Matching for `instanceof`|Native language syntax|Java 16+|
|Switch Expressions|`switch` with `yield`|Java 14+|
|Records|`record` keyword for data classes|Java 16|
|Sealed Classes|`sealed`, `permits`|Java 17|
|Virtual Threads (Project Loom)|Lightweight threads|Java 21|
|Text Blocks|Multiline string literals (`"""`)|Java 15|
|Enhanced NullPointerExceptions|Shows exact field that caused the error|Java 14|

---

### 👍 Pro Tip

- Use `java.net.http.HttpClient` for all new network calls
    
- Use `java.time.*` for all date-time operations
    
- Migrate from `javax.*` to `jakarta.*` if using modern Jakarta EE servers
    
- Avoid legacy synchronized collections unless absolutely needed


