# Chapter 12: Modules

## What Modules Are
- A **module** groups one or more packages plus a required `module-info.java` file, providing a higher level of organization than JARs/packages alone.
- The Java Platform Module System (JPMS) solves "JAR hell" — dependency conflicts and version mismatches.
- `module-info.java` must sit in the **root directory** of the module, uses the `module` keyword (not `class`/`interface`/`enum`), and module names follow package naming rules (periods allowed, dashes not allowed).
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

- **`exports`**: without it, a module can only be run standalone from the command line, not used by other modules.
- **Exported types**: all `public` classes/interfaces/enums/records are exported, along with their `public`/`protected` members. `private` and package-private members remain invisible regardless of export.
- **`requires transitive`**: if A `requires transitive` B, then any module requiring A automatically gets access to B without declaring it — reduces redundant `requires` statements down a dependency chain.
- You **cannot** declare both `requires moduleX` and `requires transitive moduleX` for the same module — it's a duplicate/redundant declaration and won't compile.
- **`opens`**: needed to allow reflection; without it, reflective access to a package is blocked. An `open module` modifier opens **all** packages — and cannot be combined with individual `opens` directives (won't compile, since it's redundant).
- **Cyclic dependencies between modules are forbidden** — Java won't compile modules that (directly or indirectly, through any number of modules) require each other. Cyclic dependencies **between packages within the same module** are allowed.

## java.base
- `java.base` is a special module (collections, math, I/O, NIO.2, concurrency, etc.) automatically available to **every** module — no `requires` needed (though including it is legal, just redundant).
- Analogous to how `java.lang` is auto-imported into every class.

## Module Names (JDK)
- JDK modules starting with **`java.*`** are APIs meant for general developer use (e.g., `java.base`, `java.sql`, `java.xml`, `java.desktop`).
- JDK modules starting with **`jdk.*`** are JDK-specific tools/internals (e.g., `jdk.jlink`, `jdk.compiler`).
- You don't need to memorize the full lists, but should recognize valid names on sight.

## Services (Provider/Locator/Consumer Pattern)
| Artifact | Part of the Service? | Directives Required |
|---|---|---|
| Service provider interface | Yes | `exports` |
| Service provider (implementation) | **No** | `requires`, `provides ... with ...` |
| Service locator | Yes | `exports`, `requires`, `uses` |
| Consumer | No | `requires` |

- A **service** = the service provider interface + any classes it references + the lookup mechanism (service locator). The **implementation is NOT part of the service**.
- `ServiceLoader<S>` is used to look up implementations: `ServiceLoader.load(Class<S>)` returns an `Iterable<S>`; `.stream()` returns a `Stream<Provider<S>>` — you must call `.get()` on each `Provider` to get the actual instance (there's no `getStream()` method).
- Both `requires` (for compilation) and `uses` (for lookup) are needed by the service locator module.
- The service provider module `requires` the interface's module and uses `provides X with Y;` — it does **not** need to export the package containing the implementation class.
- Adding a new service provider implementation requires **no recompilation** of the consumer or service locator — the `ServiceLoader` discovers it automatically at runtime (loose coupling).

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
- **Unnamed module**: a plain JAR on the **classpath**; even if it happens to contain a `module-info.java`, that file is ignored since it's not on the module path.
- Key asymmetry: **classpath code can read the module path, but module-path code cannot read the classpath.**

## Automatic Module Naming Rules
1. If `MANIFEST.MF` specifies `Automatic-Module-Name`, use that value.
2. Otherwise: strip the `.jar` extension.
3. Strip trailing version info (digits/dots, possibly with suffixes like `-RC`).
4. Replace all non-alphanumeric characters (dashes, underscores, `$`, etc.) with dots.
5. Collapse sequences of adjacent dots into one.
6. Remove any leading/trailing dot.

## Migration Strategies
| Category | Bottom-Up | Top-Down |
|---|---|---|
| Project depending on all others | Unnamed module (classpath) | Named module (module path) |
| Project with no dependencies | Named module (module path) | Automatic module (module path) |

- **Bottom-up**: start with the lowest-level (no-dependency) project, add `module-info.java`, move it to the module path; repeat upward. Mix of named modules (migrated, low-level) and unnamed modules (not yet migrated) during the process.
- **Top-down**: put *everything* on the module path first (as automatic modules), then migrate the highest-level project first by adding `module-info.java`; repeat downward. Mix of named and automatic modules during the process — no unnamed modules involved.
- Bottom-up is best when you control all JARs; top-down works when some dependencies (e.g., third-party libs) can't yet be modularized.
- Splitting a large project into modules requires eliminating **cyclic dependencies** — often solved by extracting shared code into a new common module.

## Common Exam Traps
- `module-info.java` compiles only with the `module` keyword — using `class` there fails to compile.
- Running a module needs the class name with **dots**, not slashes (`moduleName/fully.qualified.ClassName`).
- You cannot `requires` and `requires transitive` the **same module** twice.
- `open module X { opens pkg; }` does **not** compile — can't combine `open module` with explicit `opens`.
- A JAR with `module-info.java` placed on the **classpath** is still just an unnamed module — the file is ignored.
- `jimage` is for inspecting Java image files — it's not the tool for creating installers (that's `jpackage`) or runtime images (that's `jlink`).
- The service provider (implementation) itself is **not** part of "the service" — don't confuse it with the interface + locator.
- `describe-module` output always shows `requires java.base mandated`, even if not explicitly declared.
