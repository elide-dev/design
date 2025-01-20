
## ELD-3: Command-line Interface

Command-line interfaces are APIs which are used by both humans and machines. Developers depend on CLIs which are thoughtfully designed to be memorable and effective. The best CLIs -- the ones we all use, regardless of circumstance -- are well documented, respect natural CLI conventions, and work hard to be easy to understand and remember at an intuitive level.

Elide needs to work on a good CLI interface which models the "future" of how this can be expressed; there are many points of complexity:

- Language selection and settings
- VFS control; tarballs or other sources for isolated file systems
- Host access controls
- Telemetry, observability
- Script output and input
- Environment (things like Dotenv support)

### Drop-in compat

Many popular tools offer quasi-backwards-compat by adopting the CLI for tools they intend to replace, and then offering their own binary as a drop-in replacement; examples include:

- Zig's `cc`-compatible compiler interface, and friends
- CCache's drop-in shim for C compilers like gcc and clang
- Bun and Graal Node's drop-in compatibility with some Node.js flags

Elide can maybe adopt this same pattern to offer familiarity out of the box and within diverse language domains. To do this properly, though, would include _enough_ consistency across these domains (especially for shared features, like Dotenv support) that developers can move from one domain to the other. Further complicating this question, language domains and their associated options must be _composable_ to allow for multi-language contexts.
