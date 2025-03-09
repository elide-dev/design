## ELD-10: API Compliance & Conventions

Elide is a runtime and also a CLI; these classifications immediately bring to mind a suite of expectations in a developer's mind. This document attempts to enumerate and explain those expectations in a way that can be implemented.

### Elide is a CLI.

Elide's primary interface to the developer is their terminal, through Elide's use as a command-line tool. Command line tools have many established conventions, some of which are contextual in nature to the operating system where a tool runs.

Developers expect that command line tools operate in a consistent way; they are disappointed when surprised with a tool's behavior. They are delighted when a tool is thoughtfully designed.

Some expectations that developers have for CLI tools:

- **It should be aware of standard streams.** Command line tools should accept stdin where sensible, and produce actual output to stdout, and produce logging or other diagnostics to stderr, so as to function in harmony with other command line tools. This is particularly important for Unix-like operating systems where many small and interoperable tools ship with the operating system.

- **It should have output that can be controlled.** Flags like `--quiet` or `--verbose` are important to enable diagnosis or squelch irrelevant noise. Debug output should be easy to produce when a developer gets stuck.

- **It should respect their preferences.** Command-line tools should respect the locale set in the developer's environment. Command-line tools should be aware of a developer's terminal size and color preferences.

- **Installation should be simple and obvious.** Developers expect multiple install options for CLI tools, to include: one-liner shell scripts, packages distributed via GitHub or package managers (debs, rpms, etc.), and GUI installers for platforms like macOS and Windows. A command-line tool's location should be sensible (even guessable) on-disk with minimal indirection.

- **On-disk footprint and layout should be conventional.** Command-line tools should place their configuration files, cache files, data files, and other ephemera in places on-disk where the developer expects to find them.

### Elide is a runtime.

Elide's main purpose is to run guest code; this makes it a runtime. Being a runtime is a tough job: you must provide as much value to the developer as you can, while occupying as little space as possible (on-disk, in-memory, and cognitively). A runtime's code is necessarily baseline overhead for all applications built on top of it, and developers are keenly aware of this cost.

- **It should be useful.** Developing applications with Elide should be faster and easier and result in a higher quality outcome than with other frameworks or technologies. This is the single most important thing Elide should do.

- **It should give developers superpowers.** Runtime choice is often a one-way door, and developers know this. Even if we mitigate this risk, developers will shy away from trying Elide until it drives enough outsize value. Elide should lean into its strengths (especially where other runtimes cannot follow) in order to exact a distinct multiplicative benefit in a developer's workflow.

- **It should get out of the way.** Using a runtime should feel seamless for an expert developer. Once a developer has completed their initial learning curve, there should be as few interruptions as possible between them and their code. Elide should not insist upon itself.

- **It should be and feel fast.** This was already mentioned earlier, but it deserves a second mention because this is really two things: high performance systems do not always feel responsive for all use cases. Elide needs to feel fast for everyday use, but also needs to hit challenging performance targets for long-running programs like servers.

- **It should meet developers where they are and avoid breaking their code.** Elide should adopt conventions and APIs which developers find familiar, and should always prefer well-established (or standardized) APIs where possible, instead of designing new ones. API deprecation should be done with extreme care, with compatibility guarantees stated in clear and unambiguous form.

#### Elide runs JavaScript.

JavaScript runtimes bring their own package of expectations and conventions. Many of these conventions were established by Node's API, and others formed over time through influence from companies like Cloudflare. All are equally important for tapping into the grooves that already exist in a JavaScript developer's mind and perspective.

- **It should support NPM.** Deno learned this one the hard way. Runtimes really need to support the package ecosystems developers want to use: in this case, NPM, and, eventually, JSR. This support also unfortunately demands CJS.

- **It should support most of the Node API.** The Node API is enormous and developers do not expect exotic runtimes to support all of it. But there are key APIs and modules (`node:fs`, `node:path`, `node:os`, `node:process`, and others) which are much more important than their peers, and really do need support universally.

- **It should feel like a browser when it makes sense to do so.** Server-side JS runtimes now support features like `fetch(...)`, and will raise similar exceptions to those raised in a browser. Node's API mirrors browser APIs in key ways, and the gap is closing between these two in order to reduce cognitive load and implementation complexity. Elide should follow this trend.

- **It should start up fast.** V8 and JSC are very good at this, having been designed for use in browser engines with a tab- or window-oriented isolation system. Servers are not a natural fit for this pattern, but CLI tools are. This is one of Node's great insights, and one Elide should study, understand, and adopt.

#### Elide runs TypeScript.

TypeScript-native runtimes are a new addition to the JavaScript universe, most notably with Deno and Bun. Since the advent of these runtimes, execution of TypeScript directly, without a compile step, is seen as a table-stakes feature which should be included from the jump. It's also just extremely convenient and powerful to skip the compiler dependency, invocation, and sync problems inherent to compiled artifacts. This creates its own set of new expectations:

- **It should execute TypeScript directly.** Simple as. Imports should "just work." ESM and CJS should "just work."

- **It should not impose a significant performance penalty on top of JavaScript.** Bun pulls this off by stripping types from TypeScript code as it is parsed, so that it is effectively parsed in one go and then executed as JavaScript.

- **It should support JSX and TSX.** Since precompilation steps are supported in this architecture, developers have an implied second-order expectation that JSX and TSX support is also possible.
