## Common DFX Commands

## dfx Common Commands

DFINITY Command-Line Interface (dfx) is the primary tool for creating, deploying, and managing DApps on the Internet Computer (IC).

All dfx commands can be found in the [official documentation](https://internetcomputer.org/docs/current/references/cli-reference). Here, we list some commonly used commands in practice.

<br>

```bash
# Check dfx version
dfx --version
# Upgrade dfx
dfx upgrade

# Get help for a specific command
dfx help
dfx help new # View help information for the new command

dfx new hello_ic # Create a new project named hello_ic
```

<br>

### Identity-related:

```bash
# List all identities
dfx identity list
# Display the current principal-id
dfx identity get-principal
# Query the name of the developer identity
dfx identity whoami
# Display the account-id for receiving transfers
dfx ledger account-id
# Transfer from one account to another
dfx --identity xxxx ledger --network ic transfer --memo 0 --amount 0.5
# Create a new identity named neutronstarpro
dfx identity new neutronstarpro
# Use a specific identity
dfx identity use neutronstarpro
# Check the balance of the current account in ICP
dfx ledger --network ic balance
# Check the balance of the account for the identity named neutronstarpro
dfx --identity neutronstarpro ledger --network ic balance
```

<br>

### Wallet-related:

```bash
# Display the id of the current cycles wallet
dfx identity --network ic get-wallet
# Check the current balance of cycles in the wallet
dfx wallet --network ic balance
# Recharge cycles to a specific canister
dfx canister --network ic deposit-cycles 1000000 your-canister-principal
# Convert 1 ICP in dfx to cycles and recharge the canister
dfx ledger top-up $(dfx canister id your-canister-name) --amount 1
===================================================================================================
# Create a canister and convert 10 ICP from the dfx account into cycles for the canister recharge; --amount means converting the specified ICP to cycles
dfx ledger --network ic create-canister $(dfx identity get-principal) --amount 10
# Install the cycles wallet in the canister; after installation, this canister becomes a wallet-specific canister
dfx identity --network ic deploy-wallet <canister-id>
# Recharge the cycles wallet under the default identity (adjust the amount according to the situation)
dfx wallet --network ic send $(dfx identity --network ic get-wallet) 80000590000
```

<br>

### Deployment-related:

```bash
# Start the local environment
dfx start
# Clear the cache and start the local environment
dfx start --clean
# Start the local environment in the background, invisible
dfx start --background
# Start the local environment in emulator mode
dfx start --emulator
# Start the local environment in no-artificial-delay mode (the default local environment simulates the IC network with artificial delays and consensus time)
dfx start -no-artificial-delay
# Deploy to local
dfx deploy
# Stop the local container execution environment process running on the local computer
dfx stop
# ====================================================================================
# Check the current status of the IC network and whether it can be connected
dfx ping ic
# Deploy to the IC network
dfx deploy --network ic
# Deploy to the IC network, specifying 1T cycles for each canister
dfx deploy --network ic --with-cycles=1000000000000
# Deploy a single canister
dfx deploy --network ic <dapp_name>
```

<br>

### Canister-related:

```bash
# Query the canister-id named hello_assets under your own identity
dfx canister --network ic id hello_assets
# =============================================================
# Get the status of all canisters; --all can be replaced with the id or name of the canister
dfx canister --network ic status --all
# Stop running canisters
dfx canister --network ic stop --all
# Uninstall code in canisters
dfx canister --network ic uninstall-code --all
# Delete canisters and reclaim cycles
dfx canister --network ic delete --all
# Redeploy canisters, clearing all data in canisters
dfx deploy --network ic <canister_name> --mode reinstall
# Or
dfx canister install <canister_name> --mode reinstall
```

<br>

```bash
# Download DFX from Dfinity's Github repository, unzip it, and set the environment variable to use it directly
# If different versions of DFX are installed on the computer, use the environment variable to determine which version of DFX to use
# I set the DFX environment in the .profile file
export PATH=/home/neutronstarpro/.dfx:$PATH
```

<br>

```shell
DFX_CONFIG_ROOT=~/ic-root
```

Use the DFX_CONFIG_ROOT environment variable to specify a different location to store the .cache and .config subdirectories of dfx.

By default, the .cache and .config directories are located in the main directory of the development environment. For example, on macOS, the default location is in `/Users/<YOUR-USER-NAME>`. Use the DFX_CONFIG_ROOT environment variable to specify a different location for these directories.

<br>

```shell
DFX_INSTALLATION_ROOT
```

If you are not using the default location of the operating system, use the `DFX_INSTALLATION_ROOT` environment variable to specify a different location for the dfx binary.

The `.cache/dfinity/uninstall.sh` script uses this environment variable to identify the root directory of the DFINITY Canister SDK installation.

<br>

`DFX_TELEMETRY_DISABLED` is an option to choose whether telemetry data about dfx usage is collected.

By default, dfx collects anonymous information—no IP address or user information, etc.—about dfx command activities and errors. Anonymous data collection is enabled by default to improve the developer experience based on usage patterns and behavior.

To disable anonymous data collection, explicitly set the `DFX_TELEMETRY_DISABLED` environment variable to 1.

```bash
DFX_TELEMETRY_DISABLED=1
```

<br>