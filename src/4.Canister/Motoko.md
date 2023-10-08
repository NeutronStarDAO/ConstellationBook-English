Motoko is ingeniously designed with clear goals to establish an easy-to-learn, easy-to-use, and powerful programming language tailored for application development on Internet Computer. It can be safely compiled to Wasm bytecode and deployed in Canisters.

It borrows the strengths in design from languages like Java, C#, JavaScript, Swift, Pony, ML, Haskell, etc, to provide programmers with an accessible and usable development tool. Its syntax style is similar to JavaScript/TypeScript, yet more concise than JavaScript.

<br>

Motoko contains the foresight and vision of its designers for the future. After gaining a deep understanding, you may be amazed by its elegantly concise syntax design.

Motoko pursues simplicity with understandable syntax to make it easy for average developers to get started. It perfectly supports the Actor model and is an excellent choice for writing distributed programs. It aligns closely with the execution models of WebAssembly and IC to maximize hardware performance. It also considers future extensibility needs by preserving compatibility.

Motoko is a class-based object-oriented language where objects are closures. Classes can be Actors.

Some other features:

- Async construct for direct style asynchronous messaging programming.

- Structural type system, simple generics, and subtyping.

- Overflow checking numeric types, explicit conversions.

<div class="center-image">
    <img src="assets/Motoko/1.png" alt="1" style="zoom:50%;" />
</div>


So come check out Motoko's [source code](https://github.com/dfinity/motoko) and [base library](https://github.com/dfinity/motoko-base)! You can further dive into [Motoko docs](https://internetcomputer.org/docs/current/motoko/main/overview).

You can also directly deploy and test Motoko code through [Motoko Playground](https://m7sm4-2iaaa-aaaab-qabra-cai.ic0.app/) with just a browser webpage! It tremendously lowers the barrier of using Motoko and IC. Dfinity's Motoko team also frequently uses Playground to test simple Motoko code.

Also, Motoko's [package manager Vessel](https://github.com/dfinity/vessel) is a very important part of the Motoko ecosystem. With just a few lines of config code, it helps developers pull in third-party libraries into their projects, or publish their own libraries for others to use.

Beyond the above, there is [more on Motoko in the community](https://github.com/motoko-unofficial/awesome-motoko).

<br>

## The Vision of Internet Computer

To fully understand Motoko's design philosophy and goals, we first need a brief overview of the Internet Computer project that drives its inception. The DFINITY Foundation is actively pushing forward this ambitious project, with the ultimate goal of building a "World Computer", a distributed computing platform that is sufficiently powerful and open to everyone, which will provide the foundation for humanity to enter a more free and open digital civilization era.

In summary, the Internet Computer project aims to build a new kind of public computing infrastructure, similar to today's Internet, but with computing and data storage happening across many servers, instead of concentrated on servers in large data centers. This architectural difference brings tremendous decentralized advantages, making it inherently privacy-preserving, secure, and scalable.

To realize this grand vision, Dfinity needs technical innovation across layers from low-level network protocols to software ecosystem. And as the cornerstone of this layered innovation, a programming language tailored for this new platform is needed to provide direct development support for builders. Motoko comes into being, actualizing and practicing the core ideas of Internet Computer at the language level, providing crucial support to build the software foundation for this new world.

<br>

## Motoko's Design Philosophy

As a totally new built-from-scratch language, Motoko put some real thought into its overall design, with the goal of fully utilizing this language to develop for the brand spankin' new Internet Computer environment.

Motoko is a strongly-typed, actor-based language, with built-in support for orthogonal persistence and asynchronous messaging. Its productivity and safety features include automatic memory management, generics, type inference, pattern matching, and arbitrary and fixed precision arithmetic. Messaging is transparent, using Internet Computer's Candid interface definition language and wire format, enabling typed high-level cross-language interoperability.

Here are some of Motoko's core design philosophies:

* Actor-based: Motoko uses the Actor model as its core abstraction for concurrency and distributed programming. Actors are inherently concurrent and rock at asynchronous and parallel work. A Motoko program consists of multiple Actors. This model is fantastic for building distributed systems.
* Supports asynchronous messaging: Actors primarily interact by asynchronously passing messages. This communication style works great for networks, avoiding blocking and waiting.
* Built-in orthogonal persistence: Motoko greatly simplifies the complex problem of state persistence through language-level abstractions. Developers can ignore the details of saving and restoring state.
* Designed for safety: Motoko strengthens various checks and protections in its type system, runtime, and concurrency control, significantly improving program robustness and security.
* Multi-language interoperability: Motoko uses a universal interface definition language to enable safe interoperability between modules written in different languages.
* Compiles to WebAssembly: Leverages WebAssembly's advantages including portability, security, and efficiency.

As you can see, these design decisions closely match the architectural characteristics and application demands of Internet Computer, making Motoko the ideal language for developing apps on this brand new distributed platform.



## Overview of Motoko Language Features

In addition to the overall design philosophy, Motoko as a general purpose programming language also incorporates many excellent features and ideas from contemporary language design.

* Static type system: Provides compile-time type checking to catch some errors.
* Automatic memory management: Compiler handles memory through reference counting garbage collection of unused objects.
* Generics: Supports generics for increased code reuse.
* Type inference: Compiler can infer most types, but type annotations sometimes needed.
* Pattern matching: Support pattern matching on different types of values.
* Immutability: Variables are immutable by default, use var to declare mutable variables.
* Optional types: Optional types provided instead of null values.
* Overflow checking: Runtime checks for integer overflows.
* Concurrency safety: Actor message handling is atomic, maintaining atomicity within a function call.

As seen from the above language features, Motoko draws the essence from decades of programming language theory and practice, incorporating many modern features that aid reliability and productivity.



## Actor Model and Asynchronous Handling

Actors are a core abstraction in Motoko, representing concurrently executing entities. A Motoko program consists of multiple Actors that interact via asynchronous message passing, with no shared state. Each Actor has its own private mutable state. Sending a message to an Actor does not block the sender, the Actor handles messages concurrently based on its state.

This message-passing based Actor model is great for building distributed systems. On Internet Computer, Actors compile to modules that can communicate across the network, called Canisters.

To make asynchronous programming more convenient, and allow it to be expressed in sequential "direct style", another idea from programming language history and research was adopted in Motoko over 40 years ago, and fortunately has become more popular recently: Futures (also called promises in some communities), implemented in Motoko as "async values", values of type "async" produced by expressions prefixed with the "async" keyword. Notably function bodies can be async expressions, naturally superceding the more singular "async function" concept present in some other languages.

With this, deductive styles can be async as long as their results are futures. Futures can be awaited to get their value, but again only inside another async expression, akin to async/await in other programming languages.



## Orthogonal Persistence Abstraction

For building distributed systems, saving and restoring state is a critical yet complex problem. Traditionally, developers have to handle checkpointing and recovery logic themselves. But Motoko provides "orthogonal persistence" abstraction at the language level, greatly reducing the work for developers.

Orthogonal persistence refers to the separation of concerns between state persistence and business logic.

Developers only need to focus on writing the application business logic, without worrying about saving and restoring state. This complex work is abstracted away, handled by the runtime in a transparent way.

Thus, business logic and state persistence as two crossing concerns are separated. Developers only need to focus on the former and can concentrate on the application's own functionality.

This abstraction allows developers to focus more on the application's business logic itself. The underlying platform transparently handles state persistence, restoring it when actors restart, without developers having to worry about those details.

How does Motoko achieve orthogonal persistence? The key is it provides an abstraction of an "everlasting runtime".

For programmers, writing Motoko code feels like executing in an always-on runtime, where variables and state persist - no concept of program restarts.

Under the hood, the platform transparently persists state, and automatically restores it when actors restart, making execution continue as if nothing happened.

Technically, the platform tracks changes to actor mutable state, saving snapshots before each message handling, and restores on actor restarts. For immutable state, no special handling is needed.

Orthogonal persistence brings the following benefits:

* Developers can focus more on business logic without worrying about state persistence.
* Avoids duplicate labor of writing checkpointing and recovery code.
* State persistence code is centralized and optimized for efficiency.
* Simplifies building complex distributed systems.
* Lowers the bar for new developers.
* Improves testability by confining tests to single runs.

By providing the orthogonal persistence abstraction, Motoko greatly simplifies a key challenge in building distributed systems, a very clever language design innovation.



## Multi-Language Interoperability

Considering Internet Computer is an open environment where different teams may use their preferred languages, to enable smooth interoperation between modules written in different languages, Motoko adopts a universal interface description language Candid.

Each canister describes its exposed interfaces and message types in Candid. The Motoko compiler can automatically map these to internal Motoko types. Thus, canisters developed in different languages can interact through these shared interfaces while retaining type safety.

This cross-language interoperability mechanism is essential for an open distributed computing platform.



## WebAssembly

The ultimate target Motoko compiler generates is WebAssembly (abbreviated Wasm). Choosing Wasm as the target is a deliberate decision by Motoko designers, mainly based on the following considerations:

**What is WebAssembly**

WebAssembly is a future-oriented universal low-level bytecode format designed to be portable, secure, and efficient. Initially for web apps but applicable more broadly.

Wasm modules execute bytecode in a sandboxed environment agnostic of languages. The runtime uses a stack to execute instructions. Compared to VMs, Wasm is closer to hardware without language-specific optimizations.

**Why Choose Wasm**

Motoko chooses Wasm as compile target and canisters adopt Wasm mainly for:

* **Portability**. Wasm runs on any platform supporting it, not dependent on specific languages or OS.
* **Security**. Wasm's sandboxed execution guarantees code safety isolation. Critical for blockchains and decentralized apps.
* **Efficiency**. Wasm has near-native execution efficiency, crucial for the performance-sensitive IC.
* **Asynchronicity**. Wasm has built-in support for async calls, suitable for the Actor model.
* **Future-proofing**. Wasm is evolving rapidly as an industry standard, pushed by major browser vendors, so choosing it is more future-proof.

Choosing Wasm has advantages of:

* Ability to run cross-platform with excellent portability.
* Sandboxed execution providing security guarantees.
* Near-native execution efficiency critical for performance.
* Built-in async call mechanisms suiting the Actor model.
* As an evolving universal standard, Wasm has an optimistic future outlook.
* Directly compiling Motoko to IC's target runtime code, technical alignment.

Motoko's choice of Wasm as the compilation target is a very prudent design decision, leveraging Wasm's strengths to ensure Motoko's cross-platform portability, security, and efficiency. Enabling write once, run anywhere. Also increasing its industry impact. It is an exemplary engineering choice.



## Summary

Motoko is a brand new language meticulously built by Dfinity to provide programming language support for its radical Internet Computer project.

Motoko adopts innovative concepts like the Actor model, built-in orthogonal persistence in its overall design, and incorporates excellent features like static type checking, automatic memory management in the language itself. Motoko conducts extensive innovation across dimensions in its design, with the goal of reducing developer cognitive load so they can focus on building applications atop this futuristic distributed platform.

Although as a new language Motoko still has much work ahead, its ambitious vision is full of imagination for the future. Over time, Motoko will mature and eventually become a powerful tool for building the Internet Computer world.

Now with an understanding of Motoko, [go ahead and try deploying your own canister!](./DeployCanister.md)