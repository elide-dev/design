
## EDL-2: Tooling

Tools are incredibly important in any endeavor where something is built. Software developers use so many tools: package managers, test runners, linters, compilers, and runtimes, to name a few. Modern runtimes should do what they can to make this story simpler for developers.

A big drawback of multi-language codebases is the tooling involved to make them work: many repositories simply add a `package.json` and `pom.xml` side by side and call it a day. This establishes an expectation that runtime tooling is available in equal (or roughly equal) form on all developer machines: while tooling today attempts to solve this problem in many ways (Corepack, Gradle Toolchains, Nix...), one thing is clear: toolchains live outside the scope of source control in most cases, and this causes an immense amount of developer friction, especially in less mature domains like web development.

Elide should simplify this story by providing generic tools which work with all embedded languages. Packages should come from anywhere they need to come from, and simply be installable locally, with the expectation that it will "just work."

Modern tooling is available for many language ecosystems:

- Python: `uv` and `ruff`...
- JavaScript: `biome`, `prettier`, `pnpm`...
- Ruby: I am sure they exist
- JVM: Sorely needed (tools follow an older pattern here)

## JVM

This section explores JVM-specific tooling opportunities.

### Next-gen JVM Tooling

Speaking of the JVM universe: the tooling **sucks**. It is definitely the worst part of the entire language ecosystem's experience. Modern Java, Groovy, and Kotlin apps are largely confined to Maven or Gradle (ignoring esoteric options like Bazel, and outdated options like Ant).

Building a good build tool is really hard. Unlike JavaScript, there is no lightweight tooling ecosystem which could be comparable to a stack like `esbuild` and TypeScript. Newer developments like Amper do not yet offer a cohesive solution.

Elide can help solve this problem simply by adopting a unified tooling strategy and including requisite components to support JVM, to include:

- Resolving dependencies from Maven repositories (remote and local)
- Assembly of classpaths and modulepaths; awareness of modularity and encapsulation (JPMS)
- Compiling Java sources with an embedded Java compiler
- Compiling Kotlin or Groovy sources with embedded second-tier compilers
- Running Java, Kotlin, or Groovy sources directly (with an intermediate compile step)
- Running tests present in sources, JARs, or classfiles

### Integration with JVM tooling

Integration with tooling like Gradle and Maven could easily be possible. In Gradle's case, downloading dependencies is notoriously slow, and confined under the hood to outdated practices and protocols like HTTP/1.1.

The dependency story is simple: Elide could prepare a Maven-compliant layout somewhere on disk, and then provide this to Gradle or Maven as a configured repository:

**`settings.gradle.kts`**
```kotlin
plugins {
  id("dev.elide")
}

elide {
  useMavenCentral()
}
```
> This Gradle sample is just imaginary.

Other integration points worth considering:

- Maven plugin
- Kotlin Symbol Processing (KSP)
- Kotlin compiler plugin
- Java compiler plugin
- Generation of interop headers for JVM
