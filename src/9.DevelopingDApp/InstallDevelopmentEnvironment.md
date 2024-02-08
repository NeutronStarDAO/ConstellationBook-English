## Install Development Environment

### Install dfx

We need to install the DFINITY Canister SDK (dfx), which is the core tool for developing IC applications. You can download it from the [official website](https://internetcomputer.org/docs/current/developer-docs/setup/install/) and follow the instructions for installation.

Execute the following command in the terminal on Mac/Linux:

```shell
sh -ci "$(curl -fsSL https://internetcomputer.org/install.sh)"
```

For Windows systems, `dfx` itself does not support Windows. We need to install [WSL (Windows Subsystem for Linux)](https://learn.microsoft.com/en-us/windows/wsl/install) first and then continue with the installation in the Linux subsystem.

If you want to write backend code in Rust, you also need to install [CDK (Canister Development Kit)](https://github.com/dfinity/cdk-rs). However, this tutorial only uses Motoko for backend code, so we won't go into detail about Rust.

Rust is more powerful, supports more libraries, but has a steeper learning curve. Motoko is simple and easy to learn, suitable for beginners.

<br>

After installing dfx, a pair of public and private keys will be generated locally, corresponding to the developer's principal id. The private key is crucial as it represents the developer's identity and controls the Canisters under the developer's identity. The private key files are stored in `\.config\dfx\identity`. If you need to switch computers, you can transfer these private key files to the `\.config\dfx\identity` directory on the new computer.

<br>

The principal id is the developer's identity, used for deploying DApps locally and on the mainnet. Both local deployment and mainnet deployment use the same principal id (developer's identity). The only difference is that local deployment uses a local wallet, while mainnet deployment requires installing a wallet on the mainnet. This wallet is also a Canister. When developers deploy DApps, they first check the balance of Cycles in the developer's identity wallet on the mainnet, and then use the wallet Canister to create a new Canister on the mainnet and install the compiled Wasm bytecode in the new Canister.

<br>

### Install Nodejs

[https://nodejs.org](https://nodejs.org)

dfx uses Node.js to generate frontend code and dependencies. For DApps without a frontend interface, it is not mandatory, but for you following this developer journey series, it is necessary as you will explore frontend Canisters in later tutorials.

<br>

### Install VSCode

It is recommended to use [Visual Studio Code](https://code.visualstudio.com/download), a mainstream and user-friendly editor that is fast, free, and highly versatile. You can also install the Motoko plugin ([extension](https://github.com/dfinity/vscode-motoko)) for Motoko development support.

<br>

### Install Git

[https://git-scm.com/downloads](https://git-scm.com/downloads)

Many open-source code repositories are hosted on GitHub. Git is essential for publishing, downloading, and managing open-source code, making it a necessary tool for developing DApps.

<br>

Return to [Getting Started with DApp](1.GettingStartedwithDApp.md#preparation) to continue reading.