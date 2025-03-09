## ELD-1: Language

People love their favorite languages; Python makes sense to Pythonistas. JavaScript and TypeScript make sense to the web development world. Java and Kotlin developers will go on for hours about the value of the JDK and JVM.

A perfect runtime would not force language choices for its developer users. Runtimes today are a mirror image of the runtime creator's language preferences: Node.js came to be because Ryan Dahl really loved C++ and JavaScript, and wanted to use JavaScript in more places (where he had to use C++ before). That was and remains great and Node was an important contribution to the world; but a future runtime, built with Node's lessons in mind, should not restrict its value proposition to just one target language.

### Architectural considerations

By coupling themselves tightly to JavaScript, V8 and JSC confine their user base and their adaptability to new problems and use cases. Language must evolve under tight constraints, in one direction, rather than freely and in many directions. It is nearly impossible for individuals or small teams to contribute to the evolution of language under these circumstances; GraalVM's abstracted Truffle framework solves this problem.

Gains which apply to the underlying Graal compiler or Truffle framework propagate to language implementations. Advancements in memory safety and security likewise have broader impact than the same innovations in V8 or JSC.

### Competitive advantages

Access to multiple language ecosystems -- JDK, NPM, PyPI, for example -- could potentially deliver a multiplicative effect for an engineer's productivity.
