
## ELD-4: Native Interface

Elide will need to provide an interface for native integration, similar to the (native) Node-API or to V8's embedder APIs; this need has arisen several times for Elide's own internal projects or for extension demands downstream from the VM.

Similarly, Elide will need a cohesive way to build, package, and load, platform-dependent native code. The JDK/JVM framework provides us with several options for this:

- **JNI.** Today, Elide will need smooth integration with JNI for things like `sqlite` support.

- **JNA.** Java Native Access is slower than all other options, but may be the option of last resort for some libraries (OSHI).

- **FFM.** The new Foreign Memory & Foreign Function APIs in the JDK ("Project Panama") are slowly reaching a point of stability.

### Today's native approach

Elide builds Rust code within the `crates` root, producing static and shared libraries which are then consumed by the `native-image` build process; some of these libraries (and yet others from third parties) are packaged as shared libraries, to be loaded via the JVM's `loadLibrary` mechanism at runtime.

Others use a mechanism provided by GraalVM known as "Static JNI," where libraries are built as objects, and then assembled directly into the final image at runtime. Static JNI is the ideal case, as such objects do not need to be unpacked from resources before being loaded.

Static JNI relies on private APIs provided by `native-image`, so it is less reliable than other options. Within the scope of a regular `native-image` build, it is the only way to include raw object code in the final binary.

### Tomorrow's native approach

Elide should move to FFM for as many native calls as possible: FFM has shown to be faster than even JNI (which the JDK itself relies on), and will eventually be the most stable and supported option.

FFM is only fully usable on `linux-x64` for now (with regard to both upcalls and downcalls). JNI and FFM cannot be safely mixed. Thus, Elide should build an abstracted layer which delegates first to FFM, but then falls back to critical JNI where needed.

Eventually, the JNI implementation can be removed in favor of FFM-based dispatch only.
