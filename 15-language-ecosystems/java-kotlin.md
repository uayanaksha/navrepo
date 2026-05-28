# Java / Kotlin

The JVM ecosystem is mature, enterprise-heavy, and tooling-rich. Two
build systems dominate, the IDE story is excellent, and the gotchas
cluster around the classpath, JDK versions, and reflection-heavy magic.

## Build Systems: Maven vs Gradle

Two dominate; the build file tells you which:

| Build tool | Files | Notes |
|---|---|---|
| **Maven** | `pom.xml` | XML, convention-over-configuration, predictable |
| **Gradle** | `build.gradle` (Groovy) / `build.gradle.kts` (Kotlin) | Flexible, scriptable, incremental; the Kotlin DSL is increasingly common |

```bash
# Maven
mvn compile
mvn test
mvn package                 # build the artifact (jar/war)
mvn verify                  # full check incl. integration tests

# Gradle (prefer the wrapper ./gradlew — pins the Gradle version)
./gradlew build
./gradlew test
./gradlew :module:task      # task in a specific subproject
./gradlew tasks             # list available tasks
```

**Always use the wrapper** (`./gradlew`, `./mvnw`) when present — it pins
the exact build-tool version, avoiding "works on my machine" from
version drift. Kotlin uses the same build tools as Java (usually
Gradle).

## JDK Version Management

The JVM ecosystem is *very* sensitive to JDK version. Projects target a
specific one:

```bash
java -version
# Manage multiple JDKs with:
sdk list java && sdk use java 21-tem    # SDKMAN!
# or jenv, mise, asdf, or your distro's alternatives
```

- Check the target version: `pom.xml` (`maven.compiler.release`) or
  `build.gradle` (`sourceCompatibility` / `toolchain`).
- **LTS versions** (8, 11, 17, 21, …) are what most projects target.
- A mismatch causes `UnsupportedClassVersionError` or subtle bytecode
  issues. Match the project's JDK.

## IDE: JetBrains Territory

This is one ecosystem where the IDE is a genuine advantage:

- **IntelliJ IDEA** (and Android Studio, built on it) — best-in-class for
  Java/Kotlin. The refactoring, inspections, and debugging are
  exceptional. Kotlin is a JetBrains language; IntelliJ support is
  first-class.
- **Inspections** are effectively a very sophisticated linter/analyzer —
  pay attention to them.
- Eclipse and VS Code (with Java extensions) work, but IntelliJ is the
  default for serious JVM work.

See [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md).

## Testing

- **JUnit** (5, "Jupiter") — the standard test framework.
- **TestNG** — an alternative seen in some projects.
- **Mockito** — mocking.
- **AssertJ / Hamcrest** — fluent assertions.
- **Kotlin**: JUnit works, plus **Kotest**, **MockK** (Kotlin-friendly
  mocking).

```bash
mvn test
./gradlew test
./gradlew test --tests "com.example.FooTest"
```

## Build Scans & Profiling

- **Gradle build scans** (`./gradlew build --scan`) — a detailed,
  shareable report of what the build did and where time went; great for
  diagnosing slow builds.
- **Profilers**: the JVM has superb profiling — **JFR** (Java Flight
  Recorder, built in), **async-profiler** (low-overhead, flame graphs),
  and IDE-integrated profilers. **VisualVM** for a quick live view.
- **Heap analysis**: `jmap` to dump, **Eclipse MAT** to analyze leaks.
- **Thread dumps**: `jstack` for "what is it stuck on?"

Connect these to [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md).

## Classic Gotchas

### Classpath problems

The classic JVM headache. `ClassNotFoundException` /
`NoClassDefFoundError` / `NoSuchMethodError` at runtime usually mean
classpath issues:

- **Dependency conflicts** — two libraries pull in different versions of
  a third ("JAR hell" / diamond dependency). `mvn dependency:tree` or
  `./gradlew dependencies` shows the resolved versions; look for
  conflicts.
- **Version mismatch** — compiled against one version, running against
  another.

```bash
mvn dependency:tree
./gradlew dependencies
```

### JDK version surprises

Code using a newer API than the runtime JDK fails at runtime;
`UnsupportedClassVersionError` means the class was compiled for a newer
JDK than you're running. Match versions (above).

### Reflection magic

Frameworks like Spring, Hibernate, and Jackson lean heavily on
**reflection** and annotations — behavior is wired up at runtime, not
visible in the call graph. Consequences:

- **Go-to-definition lies** — the actual wiring is reflective; the IDE
  can't always show you what's injected where.
- **Errors surface at runtime**, not compile time (a missing bean, a
  misconfigured annotation).
- **Native images (GraalVM)** struggle with reflection unless configured.

When "I can't find who calls this," the answer is often "a framework
does, reflectively." Search for the annotations and config, not just
direct calls.

### `null` and `NullPointerException`

The "billion-dollar mistake" — NPEs are the classic Java runtime error.
- **Kotlin** largely fixes this with null-safety in the type system (`?`
  types) — a major reason teams adopt it.
- In Java, `Optional`, `@Nullable`/`@NonNull` annotations, and defensive
  checks help.

### Other gotchas

- **`equals()`/`hashCode()` contract** — override both or neither;
  breaking the contract corrupts hash-based collections.
- **Mutable `static` state** — thread-safety landmines.
- **Checked exceptions** — Java's verbose error handling; Kotlin drops
  them.
- **Autoboxing** — `Integer` vs `int` cache (`==` on boxed integers is a
  trap; use `.equals()`).
- **Gradle Groovy vs Kotlin DSL** — syntax differs; don't mix examples.

## Anti-Patterns

### Not using the wrapper

Running a system `gradle`/`mvn` of a different version than the project
expects. Use `./gradlew` / `./mvnw`.

### Ignoring dependency conflicts

Mysterious `NoSuchMethodError`s from version clashes left unresolved.
Read the dependency tree; pin or exclude conflicts.

### JDK version drift

Building/running on a JDK that doesn't match the project's target. Manage
JDKs explicitly (SDKMAN!/jenv/toolchains).

### Fighting framework reflection by reading the call graph

Trying to trace Spring/Hibernate wiring as if it were direct calls.
Understand the annotations and config; the framework wires it at runtime.

## See Also

- [../14-advanced/performance-investigation.md](../14-advanced/performance-investigation.md)
- [../11-tooling/editor-and-lsp.md](../11-tooling/editor-and-lsp.md)
- [cpp.md](cpp.md)
- [../09-unknown-tech/lang-vs-codebase-confusion.md](../09-unknown-tech/lang-vs-codebase-confusion.md)
