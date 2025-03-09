## ELD-5: LibC / System Interface

Elide should, through GraalVM, endeavor to support multiple LibC targets, including `glibc`, `musl`, and, ideally, [Cosmopolitan LibC](https://github.com/jart/cosmopolitan). There are good reasons to support each:

- `glibc`: Most common libc deployment in the world, which powers things like Ubuntu and Debian. Of course we need to support this target. It is built into GraalVM and is used as the default.

- `musl`: [Musl LibC](https://musl.cc) is a newer LibC implementation which focuses on smallness, correctness, and performance. It is the basis for systems like Alpine Linux, and is very popular with companies that deploy within containerized environments.

- `cosmo`: [Cosmopolitan LibC](https://github.com/jart/cosmopolitan) is a new LibC entrant which provides a new guarantee for cross-system and cross-architecture compatibility; combined with [APE (Actually Portable Executable)](https://justine.lol/ape.html), Cosmo-based binaries can be built once and run anywhere, across macOS, Windows, Linux, {Free/Open}BSD, AIX, and even BIOS or bare metal.

### Why we should support Cosmo

Firstly, Native Image builds are already heavy, and supporting Cosmo would let us eliminate some of our native builds in favor of quicker, Cosmo-targeted test builds, which can still be exercised for smoke testing on multiple platforms and architectures.

Additionally, the benefits of Cosmo would extend to native binaries built with user code, and many CLI tools or other categories of targets might prefer to ship a single binary at a slight cost to runtime performance.

Cosmo already has pretty good language support: there is a JS implementation, support for Python, and perhaps other languages. But supporting Native Image and Truffle on Cosmo would deliver Cosmo's benefits to all future Truffle languages, which would be very powerful fpr Elide and Elide's users.
