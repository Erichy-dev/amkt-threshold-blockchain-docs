## Installation Guide {#installation-guide}

This guide will walk you through the steps necessary to set up the Alongside Crypto Market Index v2 project on your local machine.

### Prerequisites {#prerequisites}

Before you begin, ensure you have the following installed on your system:

- **Git**: For cloning the repository.
- **Rust**: Required for the rebalance SDK.
- **Node.js & npm**: For managing JavaScript dependencies.
- **Foundry**: For testing smart contracts.
- **Halmos**: For symbolic testing.

### Installation Steps {#installation-steps}

#### 1. Clone the Repository

Start by cloning the project repository from GitHub:

```bash
git clone https://github.com/Alongside-Finance/amkt-v2.git
cd amkt-v2
```

#### 2. Install Dependencies

Navigate to the `contracts` directory and install the necessary dependencies:

```bash
cd contracts
npm install
```

#### 3. Set Up Foundry

Ensure Foundry is installed and set up for testing:

```bash
# Install Foundry
curl -L https://foundry.paradigm.xyz | bash
foundryup
```

#### 4. Set Up Halmos

Install Halmos for symbolic testing:

```bash
# Follow the instructions on the Halmos GitHub page for installation
# Example:
cargo install halmos
```

#### 5. Build the Contracts

Compile the smart contracts to ensure everything is set up correctly:

```bash
forge build
```

#### 6. Run Tests

Verify the setup by running the test suite:

```bash
forge test
```

#### 7. Additional Setup for Rebalance SDK

If you plan to work with the rebalance SDK, ensure Rust is installed and set up:

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup update
```

Navigate to the SDK directory and build the Rust program:

```bash
cd sdk/rebalance
cargo build
```

### Next Steps {#next-steps}

You should now have the Alongside Crypto Market Index v2 project set up on your local machine. You can start exploring the codebase, running tests, and contributing to the project.

For further information, refer to the project's [documentation](https://docs.amktdao.com/amkt-documentation/).