## ELD-12: Drop-in Homes

It is an explicit goal that Elide meet developers where they are _today_, conceptually. By solving problems with enhanced impls behind the same APIs, we can drive value immediately for projects which adopt Elide, even without code changes.

However, relying on the development of runtime stability for Elide to be useful is suboptimal. Plus, Elide has plenty of talent and potential as a toolchain in various contexts.

This document explores how Elide could potentially act as a drop-in implementation of various `*_HOME` variables, like `JAVA_HOME`, `KOTLIN_HOME`, and `ANDROID_HOME`.

### Java Home

Elide intends to ship a en embedded JVM implementation, likely Oracle GraalVM. "Shipping an embedded JVM" essentially means shipping the same files in the same layout, or some tree of links which achieves the same result.

Java Home has the following structure for GraalVM JDK 24 on my machine:

```
➜  elide git:(main) ✗ tree -L 1 $JAVA_HOME
/usr/lib/jvm/gvm.jdk24
├── GRAALVM-README.md
├── LICENSE.txt
├── bin
├── conf
├── include
├── jmods
├── legal
├── lib
├── license-information-user-manual.zip
├── man
└── release
```

- `bin` is probably easy, and could link to resolvers configured to a backing JVM, embedded or otherwise on-disk
- `conf` should replicate the exact default configuration, or link
- `include` should replicate the exact default headers, or link
- `jmods` we have to ship anyway because of Espresso's use of jimage
- `legal` is necessary of course, with elide's legal docs and oracle's as well, for their parts
- `lib` is necessary for standard libs and is already present
- `license-information-user-manual.zip` should be safe to provide
- `man` can be assembled by jlink or should symlink
- `release` will need to be amended/generated for elide, to describe the toolchain

#### Java Home: `.../lib`

The `/lib` folder in the Elide root, or in whatever Java Home root is used, must be assembled from multiple distinct sources in a cohesive way:

- `$ELIDE_HOME/resources/java/espresso-libs/lib`
- `$ELIDE_HOME/resources/java/espresso-runtime-jdk21/lib`

### Android Home

Becoming a valid Android Home is probably pretty complex. It needs more research.
