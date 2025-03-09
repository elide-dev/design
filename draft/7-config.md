## ELD-7: Infra & Config as Code

Modern software applications are a confluence of many things in addition to source code: documentation, configuration material, dependencies, and more, all come together to produce a final product.

The hermeticity and safety of an application depends on a high resolution understanding of these components. Products like Bazel and technologies like dependency lockfiles arise from frustrations with the intersection between a software project and its environment. Supply chain attacks reveal a need to understand (and address) package graphs as universal concepts, which can be linted and checked for vulnerabilities like source code.

Ultimately, what do the following have in common, or in conflict, with source code?

- YAML files describing a GitHub Actions build, or describing a Kubernetes deployment
- Package configuration files (`package.json`, etc.), dependency lockfiles
- Internationalization packages and resource bundles, or their local language equivalents

The answer is that they are accoutrements to the main meal: they are requisite inputs to a final product, but they are typically static and managed by hand. Lockfiles are an exception to this rule: these establish state which can be addressed over time.

Tools exist to code-generate some of these. And these do see use. But, generally speaking, now more than ever, an application is composed of many languages and many of these configuration profiles, and expanding our ability to address and verify these structures would relieve programmers of yet another ancillary burden.

### Prior Art

Considerations from prior art.

#### Pkl

- **[Pkl](https://pkl-lang.org/)** is a language for building configuration, and other "static" structures, or language bindings, from semi-executable code in a DSL-style friendly language.

  - Implemented as a Truffle engine, offering a natural integration route with Elide.
  - Healthy community; used within Apple so it is likely to stay supported.
  - Good relationship with the Pkl team.
  - Interoperability with JSON, YAML, Properties, PLists, etc., as well as...
  - Language bindings to several targets (Kotlin, Java, TypeScript, etc.)
  - Has a packaging, distribution, and extension system
  - Provides a useful standard library

#### Ballerina

- **[Ballerina](https://ballerina.io/)** is a language for expressing Cloud systems, microservices, and other interoperable entities, which strech easily across network and machine boundaries.

  - Implemented mostly in Java, offering a natural integration route with Elide.
  - Healthy community; several key businesses are built on top of it (Choreo, WSO).
  - Interoperability over HTTP, gRPC, and several other protocols
  - (Language bindings)?
  - Provides a useful standard library
  - Integrated visual programming features; generates flowcharts directly from code

### Applicability within Elide

Dependency specifications should be specified in Pkl, which could code-generate ephemera like `package.json` and `gradle/libs.versions.toml` for users.

Application configuration and other static material should also be specifiable in Pkl. This would allow for (limited) dynamic behavior and remote dependency support.

We should investigate integrating Ballerina as a way to describe full systems, within which code can be packaged and deployed as a single unit.

#### Ideas

- Code generation of dependency files from a central source (with modern Package URL support, etc.)
- YAML (GHA, Kubernetes) from Pkl, which is packaged as artifacts for deployment
- Import hooks to cleanly support Pkl
- System-wide or microservice descriptions in Ballerina
- Supporting Choreo as a deployment target, or Workers with some layer for Ballerina
