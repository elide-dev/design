## ELD-6: End-user Binaries

GraalVM implements a [Futamura Projection](https://speakerdeck.com/evacchi/dr-futamuras-projection-machine-from-interpreters-to-compilers-through-a-marvelous-device); it is "turtles all the way down." Accordingly, the [`native-image` binary is itself a native image, built from Java sources](https://github.com/oracle/graal/blob/master/substratevm/src/com.oracle.svm.driver/src/com/oracle/svm/driver/NativeImage.java#L1984-L1986).

Elide should pass these awesome benefits on to users. This is already an established, albeit recent, idea:

- [Bun supports standalone executables](https://bun.sh/docs/bundler/executables)
- [Node.js supports standalone executables](https://nodejs.org/api/single-executable-applications.html)
- [JDK supports `jpackage` and `jlink`](https://docs.oracle.com/en/java/javase/22/jpackage/packaging-overview.html)

To properly implement this, Elide will need to reference and embed the builder classes for `native-image`, and then dispatch a build with proper classloader support for the user's own code. Truffle computations need to be wrapped and dispatched at image build time, which may need some glue via codegen.

### Why this is important

JavaScript apps that work on serverless platforms have no problem here: their code is always interpreted, or perhaps precompiled to bytecode on the backend or on code upload, which is transparent to users. Traditional server applications in JavaScript and other languages (Python, Ruby, Java) do have a problem: when it comes time to package a container, these apps need a full runtime present within the container image, and so they typically package all of `{Python, Ruby, Java, ...}` along with their app.

More complex packaging scenarios are possible (see above), but are rarely worth the work, because they result only in slightly smaller container images, with no change in performance.

On the other hand, `native-image` output is significantly different:

- It's actually native code, with a powerful JIT behind it, which works across languages; Bun and Node are packaging their own native components along with the user's pre-interpreted bytecode. Technically speaking, Native Image is doing something fancier here.

- The final binary can be statically linked, only demanding a `libc` implementation and `zlib` underneath (offering _much_ smaller containers)

- The full runtime is not embedded within the image, only the parts that are needed; the vulnerability surface and resource demands of the image are consequently smaller

### Considerations with regard to LibC

[Support for Cosmopolitan LibC](./5-libc.md) would be a superpower when combined with the proposal expressed above: end-user binaries would enjoy the intersection of both suites of benefits, too, resulting in one native binary output which works across operating systems and architectures.
