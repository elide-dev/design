## ELD-11: JVM + Kotlin Support

Kotlin is similar in many ways to TypeScript: it was created to accelerate the evolution of JVM technologies, originally, but, like TypeScript, it has grown into a rich ecosystem in its own right. Later developments like KMP and K2 further cement Kotlin's position as a language that stands alone from Java, which is good, because Java has caught up in a lot of ways.

If we are extending the TypeScript metaphor to Kotlin in full, there are some lessons that naturally follow from current trends in the runtime space:

- Kotlin should not require a build step to run
- Maven dependencies should be fast and easy to install
- Tests, coverage, and debugging should be easy and obvious
- Startup time should be quick enough to be suitable for CLI tooling and serverless
- The best Kotlin ecosystem libs should be readily available

Bringing these features to Kotlin to match JavaScript's velocity would be a huge win. There is no other runtime which supports Kotlin directly, except for `kotlinc` + JVM.

### Kotlin Ecosystem

Kotlin is much more than a language now: many libraries are written in pure Kotlin, either targeting JVM, or targeting one or more native or web platforms via KMP (with JS, WASM, and WASI supported for web contexts). High-quality libraries exist outside of JetBrains' influence, and there is a healthy suite of "Kotlin Extensions" ("KotlinX") to the standard library which are maintained by JetBrains

At the core of KotlinX are `coroutines` and `serialization`: these two have support deeper within Kotlin. Others, like `kotlinx.html`, are simply libraries written in pure Kotlin which solve common problems.

Elide should provide the whole of KotlinX as part of the runtime environment, similar to Node's APIs. Importing and using KotlinX types should transparently work with no additional modules needing to be installed.

Ideally, the developer should be able to replace the built-in KotlinX libraries with their own versions. Since we intend to embed these as JARs that are loaded into the guest context, this should be no problem, even in native contexs.

#### Builtins

| **Name**              | **Description**                            | **Comments**              |
| --------------------- | ------------------------------------------ | ------------------------- |
| KotlinX Coroutines    | Core coroutines runtime support lib on JVM |                           |
| KotlinX Serialization | Serialization runtime and compiler plugin  | Requires compiler plug-in |
| KotlinX X             |                                            |                           |

### Architecture

Support for `kotlinc` is provided just like `javac`; this should be surfaced through Elide's command-line interface, and should accept identical arguments to the stock `kotlinc`. Elide should be aware of classpaths (as a concept), along with modulepaths, and should be able to assemble invocations of both `javac` and `java` via the various built-in Tooling APIs.

Separately, support needs to be added for the Kotlin Scripting compiler, and exploration around a custom script template which provides the flexibility needed to implement everything described here.

To the extent possible, existing Truffle tooling and support should be preferred. For example, Elide should prefer to run on Espresso, so that language interop guarantees are preserved even when running JVM languages. Elide's Kotlin supporpt should likewise work on JVM.
