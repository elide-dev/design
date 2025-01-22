
## ELD-8: Memory Safety & Security

GraalVM implements a JIT-enabled JVM in Java, which is itself a memory-safe language; as the singular basis for descendent VMs via [Futamura projection](https://en.wikipedia.org/wiki/Partial_evaluation), downstream products such as Elide also enjoy these safety guarantees.

Truffle languages which build against SVM (in native mode) still enjoy these protections, because they are inherent to the interpreter and compiler environment. Under GraalVM, these components are the same, where they would normally need to be two components which are kept in-sync (traditional JDKs, V8, and JSC all fall into this category).

[Memory safety is an extremely important topic](https://stackoverflow.blog/2024/12/30/in-rust-we-trust-white-house-office-urges-memory-safety/). VMs provide guarantees which vary:

### VM safety guarantees

- JVM is fully memory-safe, because all references are fully managed (by default), and subject to garbage collection; JNI can violate these guarantees.

- [GraalVM extends JVM's guarantees](https://medium.com/graalvm/writing-truly-memory-safe-jit-compilers-f79ad44558dd) to SVM targets and Truffle languages. Managed (guaranteed-safe) execution can even be extended to native code through [Sulong](https://github.com/oracle/graal/blob/master/sulong/README.md) ([which has other prior art](https://dl.acm.org/doi/10.1145/2786558.2786565)).

- V8 and JSC are implemented in C++ and so provide no underlying memory safety guarantees. Significant effort and attention is spent remediating escapes from guest VMs.

- Node.js and Deno are built against V8. Deno's own code is arguably memory-safe because of [Rust's borrow checker](https://verdagon.dev/grimoire/grimoire), but V8 is the underlying VM nonetheless, and so end-to-end safety cannot be guaranteed.

- Bun is based on JSC, so there is no underlying memory safety guarantee. [Zig provides weaker guarantees than Rust](https://www.scattered-thoughts.net/writing/assorted-thoughts-on-zig-and-rust/), so Bun is playing catch up in a poorer position than Deno and Node.

- CPython and CRuby are written in C, so are in a similar position to Java. Guest code is typically memory-safe but only as memory safe as guaranteed by the authors' skill in C. Like V8-based runtimes, these implementations are destined to play catch up forever. GraalPython and TruffleRuby enjoy stronger safety guarantees.
