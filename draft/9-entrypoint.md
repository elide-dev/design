## ELD-9: Entrypoint Strategy

Balancing binary size and runtime performance is inherently a tradeoff relationship in some ways. Defining a "good balance" between the two is a matter of perennial debate amongst developers. GraalVM, and indeed the JVM and Java behind it, generally prefer to produce larger binaries. Bytecode is typically very small and JARs are even often compressable, but since AOT compiling must make many assumptions to compile efficient native code from that bytecode, the resulting binaries can end up quite heavy.

The Graal team knows this and is working all the time to make binaries smaller.

All of that being said, there are good arguments on the other side, backing the case that size essentially doesn't matter to runtime performance, at least in the ways one might intuitively expect at first glance. Graal is very smart about startup and binary layout, and so the size _on-disk_ of a binary may not be representative of other performance characteristics.

### Demands of our entrypoint

Elide's entrypoint will need to take on a handful of responsibilities: it needs to work as the runtime's interface to the developer, through a well-designed CLI. It needs to accept arguments in patterns that are already extant in developers' heads, and also arguments for tools we are creating from scratch. It needs to do this across languages, ecosystems, and use cases.

Furthermore, when dispatching outside tools like uv or orogene, the entrypoint will need to properly load those tools, prepare their execution environment, and run them, all while providing a clean error/logging/diagnosis etc. experience to the invoking developer. Since CLI invocation is an up-front HOL-blocking cost for _all_ operations performed by Elide, we will want it to be fast, too.

That's a lot to put on one CLI, and so keeping our entrypoint minimal grows in importance.

#### "Entrypoint router" strategy

The initial entrypoint should act as a very lightweight "router," which accepts the user's arguments and then invokes one of a set of pre-defined routines defined in adjacent libraries.

Once the user's basic intent is determined, the binary loads a shared library for the appropriate entrypoint, passes light state/configuration to that binary through environment variables or other process injection techniques, and then passes execution control on.

Lightweight tooling binaries don't need to incur the latency to inflate the native image heap in this architecture. Likewise, Elide's language VMs need not incur the initialization latency for tooling infrastructure, and need not link into tooling at specific native symbols (Elide and binary tools are effectively decoupled).

Note: In static linkage scenarios (i.e. Cosmo and Musl), these shared libraries will need to be binaries themselves instead of relying on mechanisms like `dlopen`.

#### Poor man's polyglot layering

Until [Native Image Layers](https://github.com/oracle/graal/issues/7626) ship in full and solve all of our problems, we need a solution which lets us deliver multiple Truffle engines without incurring startup latency in an additive way.

If we simply build the JS and Python and Ruby and JVM (etc) engines together, as we have done so far in the v4 architecture, then the final binary will always see significant bloat. Binaries built this way can reach into the gigabytes, which is a very hard sell when other runtimes amount to less than 100MB for their entrypoint binary on-disk.

Instead, we can build multiple shared libraries which deliberately duplicate code to produce a combined VM:

- Pure JS/TS
- JS/TS + Python
- JS/TS + Python + JVM
- ...

And so on. These can be installed on-demand to thin the initial download size. We will see an explosion in build complexity, of course, and we must pick the variants we create carefully, but this architecture does work to produce the lowest possible startup latency in each language scenario.
