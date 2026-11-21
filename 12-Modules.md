# Chapter 12: Modules

## What Modules Are
- A **module** groups one or more packages plus a required `module-info.java` file, providing a higher level of organization than JARs/packages alone.
- The Java Platform Module System (JPMS) solves "JAR hell" — dependency conflicts and version mismatches.
- `module-info.java` must sit in the **root directory** of the module, uses the `module` keyword (not `class`/`interface`/`enum`), and module names follow package naming rules (periods allowed, dashes not allowed).
- The file name must be exactly `module-info.java`. Names like `module.java` or `moduleinfo.java` are not valid, and a package folder with only classes (such as `zoo/staff/Vet.java`) is not a module until `module-info.java` is added to the module's root (for example, directly under `zoo`, not under `zoo/staff`).
- Directives can appear in **any order** within a module declaration.

## Benefits of Modules
- Better access control (module-level, in addition to normal Java access modifiers)
- Clearer dependency management (missing dependencies caught at startup, not runtime)
- Custom, smaller Java runtime builds (vs. full 150+ MB JDK)
- Improved security (unused JDK parts can be omitted)
- Improved performance (faster startup, lower memory)
- Unique package enforcement (a package can only come from one module)

## Core Directives
| Directive | Purpose |
|---|---|
| `exports package;` | Makes a package available outside the module |
| `exports package to module;` | Restricts export to a specific module |
| `requires module;` | Declares a dependency on another module |
| `requires transitive module;` | Dependency is passed on to any module that requires this one |
| `opens package;` / `opens package to module;` | Allows reflection access to a package |
| `provides interface with impl;` | Declares a service implementation |
| `uses interface;` | Declares that this module consumes/looks up a service |

- Directive spelling matters: `exports`, `requires`, `requires transitive`, `opens`, `provides ... with ...`, and `uses`. Forms such as `export`, `require`, `use`, `provides ... by ...`, and `provides ... using ...` do not compile.
- **`exports`**: without it, a module can only be run standalone from the command line, not used by other modules. A module such as `module com.food.supplier {}` exports nothing, but a `main` method inside it can still be run directly with `java -m`.
- **Exported types**: all `public` classes/interfaces/enums/records are exported, along with their `public`/`protected` members. `private` and package-private members remain invisible regardless of export.
- **Qualified exports**: `exports package to module;` makes the package available **only** to the named module. Other modules cannot reference it. A plain `exports package;` makes it available to every module.
- **`requires transitive`**: if A `requires transitive` B, then any module requiring A automatically gets access to B without declaring it — reduces redundant `requires` statements down a dependency chain.
- Example: `puppy` requires `dog`, `dog` requires `animal`, and `animal` exports `animal.behavior`. For `puppy` to use that package, `dog` must write `requires transitive animal;` and `puppy` writes `requires dog;`. With a plain `requires animal;` in `dog`, `puppy` would not see `animal`.
- The module that owns the package is the one that must `exports` it. The directive in `animal` is `exports animal.behavior;`.
- You **cannot** declare both `requires moduleX` and `requires transitive moduleX` for the same module — it's a duplicate/redundant declaration and won't compile. The compiler error is reported on the second of the two lines.
- **`opens`**: needed to allow reflection; without it, reflective access to a package is blocked. An `open module` modifier opens **all** packages — and cannot be combined with individual `opens` directives (won't compile, since it's redundant).
- `opens` is about **reflection access**. `uses` is about declaring that the module **looks up a service** (that an API is called through `ServiceLoader`). They are not interchangeable.
- **Cyclic dependencies between modules are forbidden** — Java won't compile modules that (directly or indirectly, through any number of modules) require each other. A cycle involves at least two modules and therefore at least two `requires` statements, but it can involve any number of modules. Cyclic dependencies **between packages within the same module** are allowed.
- An unnamed module cannot be required by any module, so it cannot take part in a cyclic dependency with another module.

## java.base
- `java.base` is a special module (collections, math, I/O, NIO.2, concurrency, etc.) automatically available to **every** module — no `requires` needed (though including it is legal, just redundant).
- Analogous to how `java.lang` is auto-imported into every class.
- Describing any module shows the line `requires java.base mandated`.

## Module Names (JDK)
- JDK modules starting with **`java.*`** are APIs meant for general developer use (e.g., `java.base`, `java.sql`, `java.xml`, `java.desktop`, `java.logging`).
- JDK modules starting with **`jdk.*`** are JDK-specific tools/internals (e.g., `jdk.jlink`, `jdk.compiler`).
- You don't need to memorize the full lists, but should recognize valid names on sight.
- `java.util` is a **package** (inside `java.base`), not a module. Names such as `jdk.base`, `jdk.xerces`, and `java.core` are not JDK modules.

## Services (Provider/Locator/Consumer Pattern)
| Artifact | Part of the Service? | Directives Required |
|---|---|---|
| Service provider interface | Yes | `exports` |
| Service provider (implementation) | **No** | `requires`, `provides ... with ...` |
| Service locator | Yes | `exports`, `requires`, `uses` |
| Consumer | No | `requires` |

- A **service** = the service provider interface + any classes it references + the lookup mechanism (service locator). The **implementation is NOT part of the service**.
- A service is made of the service provider interface and the service locator. The consumer and the service provider are **not** part of the service.
- `ServiceLoader<S>` is used to look up implementations: `ServiceLoader.load(Class<S>)` returns an `Iterable<S>`; `.stream()` returns a `Stream<Provider<S>>` — you must call `.get()` on each `Provider` to get the actual instance (there's no `getStream()` method).
- A for-each loop over `ServiceLoader.load(X.class)` is equivalent to `ServiceLoader.load(X.class).stream().map(Provider::get).toList()`. Calling `.stream().toList()` gives a list of `Provider` objects, not instances.
- Both `requires` (for compilation) and `uses` (for lookup) are needed by the service locator module.
- The service provider module `requires` the interface's module and uses `provides X with Y;`, where **X is the service provider interface and Y is the implementation class** (for example `provides magic.Magic with dragon.Dragon;`). It does **not** need to export the package containing the implementation class.
- The consumer only needs `requires` (on the service locator). It does not need `uses`.
- No module needs a `requires` directive on the service provider. That is what keeps the pattern loosely coupled.
- Adding a new service provider implementation requires **no recompilation** of the consumer, service locator, service provider interface, or existing providers — the `ServiceLoader` discovers it automatically at runtime (loose coupling).

## Command-Line Tools & Key Options
### javac
- `-p` / `--module-path`: location of module JARs (replaces classpath concept for modules)
- `-d`: output directory for class files

### java
- `-p` / `--module-path`: module location
- `-m` / `--module`: module/class to run, format `moduleName/fully.qualified.ClassName` (**dot-separated class name**, not slashes — a common trick question)
- `-d` / `--describe-module`: prints module details (exports, requires, including the auto-added `requires java.base mandated`)
- `--list-modules`: lists observable modules without running the program
- `--show-module-resolution`: verbose module resolution output, then runs the program
- To run a module, you need both a module path and a module name: `java -p modules -m moduleName/fully.qualified.ClassName`. In the command, `-p` goes with the directory that holds the modules and `-m` goes with the module/class. Using `-p` on the module/class value, or leaving out `-m`, is invalid.
- Example: `java --module-path mods -m zoo.animal.talks/zoo.animal.talks.Peacocks`. The path value (such as `x` or `x-x`) can be any directory name; it is only the `-p`/`--module-path` option that matters.

### jar
- `-c` create, `-v` verbose, `-f` file, `-C` directory to package from
- `-d` / `--describe-module`: describe a module (like `java -d`, but shown from the JAR's perspective)

### jdeps
- Shows actual dependencies used in code (not just declared) — useful for pre-migration analysis of non-modular JARs
- `-s` / `-summary`: just lists modules needed (no package-level detail)
- `--jdk-internals` / `-jdkinternals`: flags usage of unsupported internal APIs (e.g., `sun.misc.Unsafe`, part of `jdk.unsupported`)
- No short form exists for `--module-path` with jdeps

### jmod
- Used only for **JMOD files** (for native libraries/content that can't go in a JAR) — rare in practice
- Modes: `create`, `extract`, `describe`, `list`, `hash`

### jlink
- Creates a **runtime image** (smaller custom JDK folder) — only works with **modular** applications
- `-p`/`--module-path`, `--add-modules`, `--output`
- Produces a directory (bin, conf, lib, etc.), not a single file

### jpackage
- Creates a **self-contained application image** (e.g., `.exe`, `.dmg`) for a specific OS
- Works with **both modular and non-modular** apps (unlike jlink)
- Modular: `--name`, `--module-path`/`-p`, `--module`/`-m`
- Non-modular: `--name`, `--input`/`-i`, `--main-class`, `--main-jar`
- `--app-version` defaults to `1.0` if omitted
- To remember which tool is which: `jpackage` → an installable file such as `.exe`; `jlink` → a directory with a smaller JDK image.

## Three Types of Modules
| Property | Named | Automatic | Unnamed |
|---|---|---|---|
| Has `module-info.java`? | Yes | No | Ignored if present |
| On module path or classpath? | Module path | Module path | Classpath |
| Exports to named modules | Only declared packages | **All** packages | **No** packages |
| Exports to automatic modules | Only declared packages | All packages | All packages |
| Readable by module-path code? | Yes | Yes | **No** |
| Readable by classpath code? | Yes | Yes | Yes |

- **Named module**: has `module-info.java`, sits on the module path.
- **Automatic module**: a plain JAR placed on the module path (no `module-info.java`); Java auto-generates a module name and auto-exports everything.
- **Unnamed module**: a plain JAR on the **classpath**; even if it happens to contain a `module-info.java`, that file is ignored since it's not on the module path. Its packages can be used by other code on the classpath, but cannot be restricted or exported to a specific module, and are not readable by named modules.
- Key asymmetry: **classpath code can read the module path, but module-path code cannot read the classpath.**
- Quick recall: named and automatic modules are on the **module path**; unnamed modules are on the **classpath**.

## Automatic Module Naming Rules
1. If `MANIFEST.MF` specifies `Automatic-Module-Name`, use that value.
2. Otherwise: strip the `.jar` extension.
3. Strip trailing version info (digits/dots, possibly with suffixes like `-RC` or `-SNAPSHOT`).
4. Replace all non-alphanumeric characters (dashes, underscores, `$`, etc.) with dots.
5. Collapse sequences of adjacent dots into one.
6. Remove any leading/trailing dot.
- Examples: `emily-1.0.0.jar` → `emily`; `emily-1.0.0-SNAPSHOT.jar` → `emily`; `emily_the_cat-1.0.0.jar` → `emily.the.cat` (underscores become dots, not dashes); `emily.$.jar` → `emily` (after collapsing and trimming dots).
- When `Automatic-Module-Name` is set, it wins over the file name. For `cat-1.2.3-RC1.jar` with `Automatic-Module-Name: dog`, other modules use `requires dog;`.

## Migration Strategies
| Category | Bottom-Up | Top-Down |
|---|---|---|
| Project depending on all others | Unnamed module (classpath) | Named module (module path) |
| Project with no dependencies | Named module (module path) | Automatic module (module path) |

- **Bottom-up**: start with the lowest-level (no-dependency) project, add `module-info.java`, move it to the module path; repeat upward. Mix of named modules (migrated, low-level) and unnamed modules (not yet migrated) during the process.
- **Top-down**: put *everything* on the module path first (as automatic modules), then migrate the highest-level project first by adding `module-info.java`; repeat downward. Mix of named and automatic modules during the process — no unnamed modules involved. In a top-down migration, every module that is not yet a named module is an **automatic** module on the **module path**.
- Bottom-up is best when you control all JARs; top-down works when some dependencies (e.g., third-party libs) can't yet be modularized.
- Splitting a large project into modules requires eliminating **cyclic dependencies** — often solved by extracting shared code into a new common module.

## Common Exam Traps
- `module-info.java` compiles only with the `module` keyword — using `class` there (for example `class dragon { exports ...; }`) fails to compile.
- Running a module needs the class name with **dots**, not slashes (`moduleName/fully.qualified.ClassName`).
- You cannot `requires` and `requires transitive` the **same module** twice.
- `open module X { opens pkg; }` does **not** compile — can't combine `open module` with explicit `opens`.
- A JAR with `module-info.java` placed on the **classpath** is still just an unnamed module — the file is ignored.
- `jimage` is for inspecting Java image files — it's not the tool for creating installers (that's `jpackage`) or runtime images (that's `jlink`).
- The service provider (implementation) itself is **not** part of "the service" — don't confuse it with the interface + locator.
- `describe-module` output always shows `requires java.base mandated`, even if not explicitly declared.
- Watch the word order in `provides`: the **interface** comes first, then `with`, then the implementation.
- Watch the directive names: `requires` (not `require`), `exports` (not `export`), `uses` (not `use`).
- A module with no `exports` is not automatically broken; it just cannot be used by other modules (it can still run as a program).
