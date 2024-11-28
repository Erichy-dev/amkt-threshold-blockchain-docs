# AMKT v2 API Documentation

## Overview

### System Relationship Diagram

![Core System Relationship Diagram](https://raw.githubusercontent.com/Alongside-Finance/amkt-v2/main/misc/diagrams/system.png)
_This diagram provides a high-level overview of the system and its core functions._

# Alongside Crypto Market Index v2

Alongside Crypto Market Index (AMKT) is a fully backed market index, providing exposure to a market-cap weighted basket of assets, to be rebalanced quarterly.

The next iteration of AMKT moves custody on-chain, relying on `Vault` to custody underlying assets, and on governance to submit accurate `Bounty` for the next set of underlying assets to rebalance.

## Table of Contents

- [AMKT v2 API Documentation](#amkt-v2-api-documentation)
  - [Overview](#overview)
    - [System Relationship Diagram](#system-relationship-diagram)
- [Alongside Crypto Market Index v2](#alongside-crypto-market-index-v2)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview-1)
    - [System Relationship Diagram](#system-relationship-diagram-1)
    - [AMKT](#amkt)
    - [Core](#core)
    - [Governance](#governance)
  - [Local Development](#local-development)
    - [Tests](#tests)
    - [Rebalance](#rebalance)
  - [Links](#links)
  - [Installation Guide {#installation-guide}](#installation-guide-installation-guide)
    - [Prerequisites {#prerequisites}](#prerequisites-prerequisites)
    - [Installation Steps {#installation-steps}](#installation-steps-installation-steps)
      - [1. Clone the Repository](#1-clone-the-repository)
      - [2. Install Dependencies](#2-install-dependencies)
      - [3. Set Up Foundry](#3-set-up-foundry)
      - [4. Set Up Halmos](#4-set-up-halmos)
      - [5. Build the Contracts](#5-build-the-contracts)
      - [6. Run Tests](#6-run-tests)
      - [7. Additional Setup for Rebalance SDK](#7-additional-setup-for-rebalance-sdk)
    - [Next Steps {#next-steps}](#next-steps-next-steps)
  - [Core Contracts {#core-contracts}](#core-contracts-core-contracts)
    - [Vault Contract {#vault-contract}](#vault-contract-vault-contract)
      - [State Variables](#state-variables)
      - [Core Functions](#core-functions)
        - [Virtual Units Management](#virtual-units-management)
        - [Asset Management](#asset-management)
          - [Token Operations](#token-operations)
          - [Fee Management](#fee-management)
          - [Emergency Controls](#emergency-controls)
    - [Issuance Contract {#issuance-contract}](#issuance-contract-issuance-contract)
      - [Core Functions](#core-functions-1)
        - [Token Issuance](#token-issuance)
        - [Token Redemption](#token-redemption)
    - [InvokeableBounty Contract {#invokeable-bounty-contract}](#invokeablebounty-contract-invokeable-bounty-contract)
      - [Core Functions](#core-functions-2)
        - [Bounty Fulfillment](#bounty-fulfillment)
        - [Bounty Struct](#bounty-struct)
    - [Events {#events}](#events-events)
      - [Vault Events](#vault-events)
      - [Issuance Events](#issuance-events)
      - [Bounty Events](#bounty-events)
    - [Modifiers](#modifiers)
    - [Error Codes](#error-codes)
    - [Integration Examples](#integration-examples)
      - [Issuing AMKT](#issuing-amkt)
      - [Redeeming AMKT](#redeeming-amkt)
    - [Security Considerations {#security-considerations}](#security-considerations-security-considerations)
    - [Gas Optimization Notes](#gas-optimization-notes)
  - [AMKT v2 Rebalance API Documentation](#amkt-v2-rebalance-api-documentation)
    - [Overview](#overview-2)
    - [Rebalance SDK](#rebalance-sdk)
      - [Installation](#installation)
      - [Core Components](#core-components)
        - [1. Bounty Structure](#1-bounty-structure)
      - [2. Rebalance Configuration](#2-rebalance-configuration)
  - [Smart Contract Interface](#smart-contract-interface)
    - [InvokeableBounty Contract](#invokeablebounty-contract)
      - [Setting a New Bounty](#setting-a-new-bounty)
      - [Fulfilling a Bounty](#fulfilling-a-bounty)
  - [Rebalance Process](#rebalance-process)
    - [1. Preparation Phase](#1-preparation-phase)
    - [2. Governance Proposal](#2-governance-proposal)
    - [3. Execution](#3-execution)
  - [Events](#events)
  - [Error Codes](#error-codes-1)
  - [Usage Examples](#usage-examples)
    - [1. Calculate Rebalance Requirements](#1-calculate-rebalance-requirements)
    - [2. Submit Rebalance Proposal](#2-submit-rebalance-proposal)
    - [3. Execute Rebalance](#3-execute-rebalance)
  - [Security Considerations](#security-considerations)
  - [Best Practices](#best-practices)
  - [Governance API {#governance-api}](#governance-api-governance-api)
    - [Overview {#governance-overview}](#overview-governance-overview)
    - [Core Components {#governance-components}](#core-components-governance-components)
      - [TimelockController {#timelock-controller}](#timelockcontroller-timelock-controller)
        - [Key Parameters](#key-parameters)
        - [Core Functions](#core-functions-3)
      - [Governance Contract {#governance-contract}](#governance-contract-governance-contract)
        - [Voting Parameters](#voting-parameters)
      - [Key Functions](#key-functions)
    - [Governance Actions {#governance-actions}](#governance-actions-governance-actions)
      - [Rebalance Process {#rebalance-process}](#rebalance-process-rebalance-process)
    - [Vault Configuration](#vault-configuration)
    - [Role Management](#role-management)
    - [Security Measures {#security-measures}](#security-measures-security-measures)
      - [Governance Multisig {#governance-multisig}](#governance-multisig-governance-multisig)
        - [Capabilities](#capabilities)
    - [Participation Guide {#participation-guide}](#participation-guide-participation-guide)
      - [1. Token Voting](#1-token-voting)
    - [2. Proposal Creation](#2-proposal-creation)
    - [3. Voting Process](#3-voting-process)
    - [Events {#governance-events}](#events-governance-events)
  - [Integration Examples](#integration-examples-1)
    - [Creating a Proposal](#creating-a-proposal)
    - [Voting on a Proposal](#voting-on-a-proposal)
  - [Error Codes](#error-codes-2)
  - [Events API Documentation](#events-api-documentation)
    - [Overview](#overview-3)
    - [Core Events](#core-events)
      - [Vault Events](#vault-events-1)
        - [TokensDeposited](#tokensdeposited)
        - [TokensWithdrawn](#tokenswithdrawn)
      - [Rebalance Events](#rebalance-events)
        - [RebalanceInitiated](#rebalanceinitiated)
        - [RebalanceCompleted](#rebalancecompleted)
      - [Governance Events](#governance-events)
        - [ProposalCreated](#proposalcreated)
        - [ProposalExecuted](#proposalexecuted)
    - [Emergency Events](#emergency-events)
        - [EmergencyPaused](#emergencypaused)
        - [EmergencyUnpaused](#emergencyunpaused)
    - [Usage Examples](#usage-examples-1)
      - [Listening for Events (Web3.js)](#listening-for-events-web3js)
      - [Querying Historical Events (Ethers.js)](#querying-historical-events-ethersjs)
    - [Event Monitoring Best Practices](#event-monitoring-best-practices)
    - [Security Considerations](#security-considerations-1)
  - [Error Codes Documentation](#error-codes-documentation)
    - [Overview](#overview-4)
    - [Core Errors](#core-errors)
      - [Vault Errors](#vault-errors)
        - [InsufficientBalance](#insufficientbalance)
        - [InvalidToken](#invalidtoken)
        - [MaxCapacityExceeded](#maxcapacityexceeded)
      - [Rebalance Errors](#rebalance-errors)
        - [InvalidBounty](#invalidbounty)
        - [ExpiredBounty](#expiredbounty)
        - [SlippageExceeded](#slippageexceeded)
      - [Governance Errors](#governance-errors)
        - [UnauthorizedProposer](#unauthorizedproposer)
        - [InvalidProposalState](#invalidproposalstate)
    - [Access Control Errors](#access-control-errors)
        - [Unauthorized](#unauthorized)
        - [InvalidMultisig](#invalidmultisig)
    - [Error Handling Examples](#error-handling-examples)
      - [Contract Level](#contract-level)
      - [Client Level (TypeScript)](#client-level-typescript)
    - [Error Handling Best Practices](#error-handling-best-practices)
    - [Error Monitoring](#error-monitoring)
    - [Security Implications](#security-implications)
  - [Testing Documentation {#testing-documentation}](#testing-documentation-testing-documentation)
    - [Overview {#testing-overview}](#overview-testing-overview)
    - [Foundry Tests {#foundry-tests}](#foundry-tests-foundry-tests)
      - [Setup](#setup)
      - [Test Structure](#test-structure)
      - [Key Test Commands](#key-test-commands)
    - [Symbolic Testing with Halmos {#symbolic-testing}](#symbolic-testing-with-halmos-symbolic-testing)
      - [Setup](#setup-1)
      - [Key Test Areas](#key-test-areas)
        - [1. State Invariants](#1-state-invariants)
        - [2. Access Control](#2-access-control)
    - [Test Coverage {#test-coverage}](#test-coverage-test-coverage)
    - [Testing Best Practices {#testing-best-practices}](#testing-best-practices-testing-best-practices)
      - [1. Test Organization](#1-test-organization)
      - [2. Common Test Scenarios {#test-scenarios}](#2-common-test-scenarios-test-scenarios)
        - [Vault Testing](#vault-testing)
        - [Issuance Testing](#issuance-testing)
        - [Bounty Testing](#bounty-testing)
      - [3. Fuzzing Parameters {#fuzzing-parameters}](#3-fuzzing-parameters-fuzzing-parameters)
  - [CI/CD Integration and Testing Guide](#cicd-integration-and-testing-guide)
    - [CI/CD Integration](#cicd-integration)
      - [GitHub Actions Workflow](#github-actions-workflow)
    - [Debugging Tools](#debugging-tools)
      - [Trace Logging](#trace-logging)
      - [Gas Snapshots](#gas-snapshots)
      - [Debug Console](#debug-console)
    - [Common Issues and Solutions](#common-issues-and-solutions)
      - [Gas Estimation Failures](#gas-estimation-failures)
      - [Memory Issues](#memory-issues)
      - [Stack Too Deep](#stack-too-deep)
      - [Additional Best Practices](#additional-best-practices)
  - [Security Considerations](#security-considerations-2)
    - [Overview](#overview-5)
    - [Access Control](#access-control)
      - [Role-Based Permissions](#role-based-permissions)
        - [Core Roles](#core-roles)
        - [Implementation Example](#implementation-example)
      - [Time Locks](#time-locks)
        - [Governance Delay](#governance-delay)
    - [Value Protection](#value-protection)
      - [Slippage Control](#slippage-control)
      - [Circuit Breakers](#circuit-breakers)
    - [Smart Contract Security](#smart-contract-security)
      - [Reentrancy Protection](#reentrancy-protection)
      - [Secure Token Transfers](#secure-token-transfers)
    - [Emergency Procedures](#emergency-procedures)
      - [Emergency Shutdown](#emergency-shutdown)
      - [Fund Recovery](#fund-recovery)
    - [Monitoring and Alerts](#monitoring-and-alerts)
      - [Critical Events](#critical-events)
      - [Monitoring Implementation](#monitoring-implementation)
    - [Security Best Practices](#security-best-practices)
  - [Security Considerations](#security-considerations-3)
    - [Overview](#overview-6)
    - [Access Control](#access-control-1)
      - [Role-Based Permissions](#role-based-permissions-1)
        - [Core Roles](#core-roles-1)
        - [Implementation Example](#implementation-example-1)
      - [Time Locks](#time-locks-1)
        - [Governance Delay](#governance-delay-1)
    - [Value Protection](#value-protection-1)
      - [Slippage Control](#slippage-control-1)
      - [Circuit Breakers](#circuit-breakers-1)
    - [Smart Contract Security](#smart-contract-security-1)
      - [Reentrancy Protection](#reentrancy-protection-1)
      - [Secure Token Transfers](#secure-token-transfers-1)
    - [Emergency Procedures](#emergency-procedures-1)
      - [Emergency Shutdown](#emergency-shutdown-1)
      - [Fund Recovery](#fund-recovery-1)
    - [Monitoring and Alerts](#monitoring-and-alerts-1)
      - [Critical Events](#critical-events-1)
      - [Monitoring Implementation](#monitoring-implementation-1)
    - [Security Best Practices](#security-best-practices-1)
  - [Best Practices](#best-practices-1)
    - [Overview](#overview-7)
    - [Code Development](#code-development)
      - [Solidity Patterns](#solidity-patterns)
        - [Safe Math Operations](#safe-math-operations)
        - [Check-Effects-Interactions Pattern](#check-effects-interactions-pattern)
      - [Code Organization](#code-organization)
        - [Module Structure](#module-structure)
    - [Testing Guidelines](#testing-guidelines)
      - [Unit Testing](#unit-testing)
      - [Integration Testing](#integration-testing)
    - [Documentation Standards](#documentation-standards)
      - [Code Documentation](#code-documentation)
        - [Event Documentation](#event-documentation)
    - [Deployment Process](#deployment-process)
      - [Deployment Checklist](#deployment-checklist)
      - [Configuration Management](#configuration-management)
    - [Monitoring and Maintenance](#monitoring-and-maintenance)
      - [Health Checks](#health-checks)
      - [Performance Optimization](#performance-optimization)
    - [Upgrade Procedures](#upgrade-procedures)
      - [Safe Upgrade Pattern](#safe-upgrade-pattern)
    - [Error Handling](#error-handling)
      - [Error Recovery](#error-recovery)
    - [Community Guidelines](#community-guidelines)

## Overview

### System Relationship Diagram

![Core System Relationship Diagram](https://raw.githubusercontent.com/Alongside-Finance/amkt-v2/main/misc/diagrams/system.png)
_This diagram provides a high-level overview of the system and its core functions._

### AMKT

AMKT is an [Upgradeable](contracts/lib/openzeppelin-contracts-upgradeable/contracts/token/ERC20/ERC20Upgradeable.sol), [Votable](contracts/lib/openzeppelin-contracts-upgradeable/contracts/token/ERC20/extensions/ERC20VotesUpgradeable.sol), ERC20 token on [Ethereum Mainnet](https://etherscan.io/token/0xf17a3fe536f8f7847f1385ec1bc967b2ca9cae8d).

### Core

The core system aims to be modular and minimal for maximal safety and extensibility.

- [Vault](contracts/src/Vault.sol): Source of truth for the index and custodian of assets.
  - `virtualUnits()` defines the amount of each asset required in the index.
  - Issuance is authorized to call `invokeERC20s()`, `invokeMint()`, and `invokeBurn()`.
  - InvokeableBounty is authorized to call `invokeERC20s()`, and `invokeSetNominals()`.
  - Fee Recipient is authorized to call `tryInflation()`, which will mint accrued fees to them and adjust `virtualUnits`to account for minted tokens.
  - Emergency Multisig is authorized to call `setEmergency`, which will pause token issuance and rebalance, but cannot not pause redemptions.
  - Governance owns Vault via TimelockController. It's able to change the configurations of the vault, including fee rate, as well as roles.
- [Issuance](contracts/src/invoke/Issuance.sol): Entry point for issuing and redeeming AMKT.

  - `issue()` and `redeem()` are permisionless functions allowing conversion of underlying assets and AMKT.

- [InvokeableBounty](contracts/src/invoke/Bounty.sol): Entry point for rebalancing the index.
  - `fulfillBounty()` is a permissioned function only callable by `fulfiller` of the `Bounty`. It calculates amount of tokens to exchange based on the `Bounty` and facilitates the exchange. It also modifies `virtualUnits` to reflect the rebalance.
  - [ActiveBounty](contracts/src/invoke/ActiveBounty.sol) maintains the currently active bounty. Governance is authorized to call `setHash()`, which will make a `Bounty` active.

### Governance

Governance is responsible for facilitating rebalances, configuring the Vault, and upgrading AMKT. Users can participate in governance with AMKT via [Tally](https://tally.xyz/gov/amkt). Every governance action besides rebalances is subject to a minimum delay of 4 days, enforced by the `TimelockController`.

To prevent against a malicious token Governance takeover, the Governance Multisig is the only entity authorized to cancel and execute transactions scheduled in `TimelockController`.

## Local Development

### Tests

This repo primarily uses [Foundry](https://github.com/gakonst/foundry) for testing.

`cd contracts && forge test`

Additionally, [Halmos](https://github.com/a16z/halmos) is used for symbolic testing.

`cd contracts && halmos`

### Rebalance

`sdk/rebalance` contains an example Rust program to assist users with preparing a rebalance proposal to Governance.

---

## Links

- [Website](https://alongside.xyz/)
- [Twitter/X](https://twitter.com/alongsidefi)
- [Docs](https://docs.amktdao.com/amkt-documentation/)
- [Bug Bounty](https://immunefi.com/bounty/alongside/)
- [DAO Forum](https://discuss.amktdao.com/)
- [DAO Governance](https://tally.xyz/gov/amkt)

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

## Core Contracts {#core-contracts}

### Vault Contract {#vault-contract}
**Location**: `contracts/src/Vault.sol`

#### State Variables
```solidity
address public feeRecipient;
uint256 public feeRate;
mapping(address => uint256) public virtualUnits;
bool public emergency;
```

#### Core Functions

##### Virtual Units Management
```solidity
function virtualUnits(address token) external view returns (uint256)
```
- **Description**: Returns the required amount of a specific token for the index
- **Parameters**: 
  - `token`: ERC20 token address
- **Returns**: Amount of tokens required (in base units)
- **Access**: Public view
- **Purpose**: Provides real-time token weightings for index composition
- **Key Aspects**:
  - Returns base units (e.g., wei for ETH)
  - Used in issuance/redemption calculations
  - Critical for maintaining index parity
  - Updated during rebalancing

##### Asset Management
```solidity
function invokeERC20s(
    address[] calldata tokens,
    address[] calldata destinations,
    uint256[] calldata amounts
) external onlyAuthorized
```
- **Description**: Transfers multiple ERC20 tokens to specified destinations
- **Parameters**:
  - `tokens`: Array of ERC20 token addresses
  - `destinations`: Array of recipient addresses
  - `amounts`: Array of token amounts
- **Access**: Only authorized addresses (Issuance & InvokeableBounty)
- **Reverts**: If arrays have different lengths or transfers fail
- **Security Features**:
  - Multi-token batch processing
  - Authorized access control
  - Array length validation
  - Failed transfer protection
  - Gas optimization for bulk operations

###### Token Operations
```solidity
function invokeMint(address to, uint256 amount) external onlyIssuance
```
- **Description**: Mints AMKT tokens
- **Parameters**:
  - `to`: Recipient address
  - `amount`: Amount to mint
- **Access**: Only Issuance contract
- **Implementation Details**:
  - Restricted to Issuance contract
  - Maintains supply control
  - Emits tracking events
  - Validates recipient address
  - Ensures sufficient capacity

```solidity
function invokeBurn(address from, uint256 amount) external onlyIssuance
```
- **Description**: Burns AMKT tokens
- **Parameters**:
  - `from`: Address to burn from
  - `amount`: Amount to burn
- **Access**: Only Issuance contract
- **Critical Aspects**:
  - Balance verification
  - Authorization checks
  - Supply management
  - Event emission
  - State consistency

###### Fee Management
```solidity
function tryInflation() external returns (uint256 mintedAmount)
```
- **Description**: Mints accrued fees and adjusts virtualUnits
- **Returns**: Amount of tokens minted
- **Access**: Only fee recipient
- **Events Emitted**: `InflationMinted(uint256 amount)`
- **Mechanism**:
  - Calculates accrued fees
  - Updates virtual units
  - Mints new tokens
  - Distributes to recipient
  - Maintains economic model

###### Emergency Controls
```solidity
function setEmergency(bool state) external onlyEmergencyMultisig
```
- **Description**: Sets emergency state to pause/unpause operations
- **Parameters**:
  - `state`: Boolean emergency state
- **Access**: Only emergency multisig
- **Events Emitted**: `EmergencySet(bool state)`
- **Safety Features**:
  - Multisig control
  - Immediate effect
  - Selective function pausing
  - Event logging
  - Recovery procedures

### Issuance Contract {#issuance-contract}
**Location**: `contracts/src/invoke/Issuance.sol`

#### Core Functions

##### Token Issuance
```solidity
function issue(uint256 amktAmount, address recipient) external returns (uint256[] memory)
```
- **Description**: Issues AMKT tokens in exchange for underlying assets
- **Parameters**:
  - `amktAmount`: Amount of AMKT to issue
  - `recipient`: Address to receive AMKT
- **Returns**: Array of token amounts required
- **Access**: Public
- **Events Emitted**: `Issue(address indexed recipient, uint256 amount)`
- **Process Flow**:
  - Calculates token requirements
  - Validates balances
  - Transfers underlying assets
  - Mints AMKT tokens
  - Updates state
  - Emits events

##### Token Redemption
```solidity
function redeem(uint256 amktAmount, address recipient) external returns (uint256[] memory)
```
- **Description**: Redeems AMKT tokens for underlying assets
- **Parameters**:
  - `amktAmount`: Amount of AMKT to redeem
  - `recipient`: Address to receive underlying assets
- **Returns**: Array of token amounts returned
- **Access**: Public
- **Events Emitted**: `Redeem(address indexed redeemer, uint256 amount)`
- **Execution Steps**:
  - Verifies AMKT balance
  - Calculates output amounts
  - Burns AMKT tokens
  - Transfers underlying assets
  - Updates protocol state
  - Generates events

### InvokeableBounty Contract {#invokeable-bounty-contract}
**Location**: `contracts/src/invoke/Bounty.sol`

#### Core Functions

##### Bounty Fulfillment
```solidity
function fulfillBounty(
    Bounty calldata bounty,
    bytes32[] calldata proof
) external onlyFulfiller returns (bool)
```
- **Description**: Executes rebalancing based on provided bounty
- **Parameters**:
  - `bounty`: Struct containing rebalance parameters
  - `proof`: Merkle proof validating the bounty
- **Returns**: Success boolean
- **Access**: Only authorized fulfiller
- **Events Emitted**: `BountyFulfilled(bytes32 bountyHash)`
- **Verification Process**:
  - Validates Merkle proof
  - Checks deadline
  - Verifies token addresses
  - Confirms amounts
  - Updates virtual units
  - Executes transfers

##### Bounty Struct
```solidity
struct Bounty {
    address[] tokens;
    uint256[] nominals;
    uint256 deadline;
    bytes32 root;
}
```
- **tokens**: Array of token addresses in the index
- **nominals**: Array of new token amounts
- **deadline**: Timestamp when bounty expires
- **root**: Merkle root for validation
- **Design Considerations**:
  - Gas efficiency
  - Data integrity
  - Upgrade compatibility
  - Security verification
  - State consistency

### Events {#events}

#### Vault Events
```solidity
event InflationMinted(uint256 amount);
event EmergencySet(bool state);
event VirtualUnitsSet(address[] tokens, uint256[] amounts);
```
- **Usage**:
  - Off-chain tracking
  - State verification
  - Analytics support
  - User notifications
  - Audit trail

#### Issuance Events
```solidity
event Issue(address indexed recipient, uint256 amount);
event Redeem(address indexed redeemer, uint256 amount);
```
- **Monitoring**:
  - Transaction tracking
  - Volume analysis
  - User activity
  - System health
  - Market dynamics

#### Bounty Events
```solidity
event BountyFulfilled(bytes32 indexed bountyHash);
event BountySet(bytes32 indexed bountyHash);
```
- **Applications**:
  - Rebalance tracking
  - Performance monitoring
  - Historical analysis
  - System verification
  - Automation triggers

### Modifiers

```solidity
modifier onlyAuthorized()
modifier onlyIssuance()
modifier onlyEmergencyMultisig()
modifier onlyFulfiller()
modifier nonEmergency()
```

### Error Codes

```solidity
error Unauthorized();
error EmergencyPaused();
error InvalidArrayLength();
error TransferFailed();
error InvalidBounty();
error ExpiredBounty();
error InvalidProof();
```

### Integration Examples

#### Issuing AMKT
```solidity
// 1. Approve token transfers
for (uint i = 0; i < tokens.length; i++) {
    IERC20(tokens[i]).approve(address(issuance), amounts[i]);
}

// 2. Issue AMKT
uint256 amktAmount = 1e18; // 1 AMKT
issuance.issue(amktAmount, msg.sender);
```

#### Redeeming AMKT
```solidity
// 1. Approve AMKT transfer
IAMKT(amktAddress).approve(address(issuance), amktAmount);

// 2. Redeem AMKT
issuance.redeem(amktAmount, msg.sender);
```

### Security Considerations {#security-considerations}

1. **Access Control**
   - Critical functions are protected by role-based access
   - Emergency pause mechanism for risk mitigation
   - Timelocked governance actions

2. **Input Validation**
   - Array length checks
   - Bounty validation through Merkle proofs
   - Deadline checks for bounties

3. **Asset Safety**
   - Transfer validations
   - Fee calculations with overflow protection
   - Emergency controls cannot block redemptions

4. **Rebalancing Safety**
   - Bounty verification through Merkle proofs
   - Deadline enforcement
   - Only authorized fulfiller can execute

### Gas Optimization Notes

1. Use of array-based batch operations for token transfers
2. Minimal storage operations
3. Efficient validation checks
4. Optimized struct packing

## AMKT v2 Rebalance API Documentation

### Overview

The rebalance system allows for periodic updates to the index composition through a secure, governance-controlled process using the SDK and smart contracts. This process ensures:
- Transparent and verifiable index updates
- Protection against price manipulation
- Efficient execution through bounty mechanisms
- Multi-layer security checks

### Rebalance SDK

The SDK provides programmatic access to rebalancing functionality, enabling automated calculations and proposal generation.

#### Installation
```bash
cd sdk/rebalance
cargo build
```

#### Core Components

##### 1. Bounty Structure
The bounty system incentivizes efficient index rebalancing while maintaining security:
```rust
pub struct Bounty {
    tokens: Vec<Address>,    // List of token addresses to rebalance
    nominals: Vec<u256>,     // New token quantities after rebalance
    deadline: u256,          // Timestamp for bounty expiration (prevents stale execution)
    root: [u8; 32]          // Merkle root for cryptographic validation
}
```

#### 2. Rebalance Configuration
Configuration parameters that define the rebalance constraints and targets:
```rust
pub struct RebalanceConfig {
    target_weights: HashMap<Address, Decimal>,  // Desired portfolio allocation
    current_holdings: HashMap<Address, u256>,   // Existing token balances
    price_data: HashMap<Address, Decimal>,      // Current market prices
    slippage_tolerance: Decimal                 // Maximum acceptable price impact
}
```

## Smart Contract Interface

### InvokeableBounty Contract
The core contract managing rebalance execution with built-in security checks.

#### Setting a New Bounty
```solidity
function setHash(bytes32 bountyHash) external onlyGovernance
```

#### Fulfilling a Bounty
```solidity
function fulfillBounty(
    Bounty calldata bounty,
    bytes32[] calldata proof
) external onlyFulfiller returns (bool)
```

## Rebalance Process

The rebalance workflow consists of three critical phases:

### 1. Preparation Phase
Involves careful calculation of required trades while considering:
- Market depth and liquidity
- Price impact
- Gas optimization
```rust
// Example SDK usage for preparing a rebalance
let rebalance = RebalanceCalculator::new(config)
    .calculate_required_trades()
    .generate_bounty();
```

### 2. Governance Proposal
Ensures community oversight and validation of proposed changes:
```solidity
// Submit bounty through governance with proper checks
function proposeBountyUpdate(
    address[] calldata tokens,
    uint256[] calldata nominals,
    uint256 deadline,
    bytes32 root
) external returns (uint256 proposalId)
```

### 3. Execution
Controlled execution by authorized fulfillers with verification:
```solidity
// After governance approval, fulfiller executes with proof validation
function executeBounty(
    Bounty memory bounty,
    bytes32[] memory proof
) external
```

## Events

```solidity
event BountySet(bytes32 indexed bountyHash);
event BountyFulfilled(
    bytes32 indexed bountyHash,
    address indexed fulfiller
);
```

## Error Codes

```solidity
error InvalidBounty();
error ExpiredBounty();
error InvalidProof();
error UnauthorizedFulfiller();
```

## Usage Examples

### 1. Calculate Rebalance Requirements
```rust
use amkt_sdk::rebalance::{RebalanceCalculator, Config};

fn prepare_rebalance() -> Bounty {
    let config = Config {
        target_weights: vec![
            (token_a, 0.5),
            (token_b, 0.3),
            (token_c, 0.2)
        ],
        slippage_tolerance: 0.01,
        // ... other config
    };

    RebalanceCalculator::new(config)
        .calculate_required_trades()
        .generate_bounty()
}
```

### 2. Submit Rebalance Proposal
```typescript
// Example using ethers.js
async function submitRebalanceProposal(bounty: Bounty) {
    const proposal = await governance.propose(
        [bounty.address],
        [0],
        [bounty.calldata],
        "Monthly Index Rebalance"
    );
    return proposal.wait();
}
```

### 3. Execute Rebalance
```solidity
// Example fulfiller implementation
function executeRebalance(
    Bounty calldata bounty,
    bytes32[] calldata proof
) external {
    require(msg.sender == fulfiller, "Unauthorized");
    bountyContract.fulfillBounty(bounty, proof);
}
```

## Security Considerations

1. **Validation Checks**
   - Merkle proof verification ensures data integrity
   - Deadline enforcement prevents stale execution
   - Slippage protection guards against price manipulation

2. **Access Control**
   - Governance-controlled bounty setting prevents unauthorized changes
   - Restricted fulfiller access ensures proper execution
   - Timelock delays allow community review

3. **Economic Security**
   - Maximum weight changes prevent dramatic shifts
   - Price impact limits protect against market manipulation
   - Token whitelist ensures index quality

## Best Practices

1. **Pre-execution Validation**
```rust
fn validate_rebalance(bounty: &Bounty) -> Result<(), Error> {
    // Verify token addresses
    // Check weight constraints
    // Validate price impact
    // Ensure sufficient liquidity
}
```

2. **Monitoring**
```rust
fn monitor_execution(tx_hash: H256) -> Result<(), Error> {
    // Track transaction status
    // Monitor price impact
    // Log execution details
}
```

3. **Emergency Procedures**
```solidity
// Emergency pause if needed
function emergencyPause() external onlyEmergencyMultisig {
    vault.setEmergency(true);
}
```

## Governance API {#governance-api}

### Overview {#governance-overview}

The AMKT governance system enables decentralized control over the index through a combination of token voting, time-locked execution, and multi-sig security measures. This multi-layered approach ensures:
- Transparent and democratic decision-making through token-weighted voting
- Protection against malicious proposals via timelock delays
- Emergency safeguards through multi-signature controls

### Core Components {#governance-components}

#### TimelockController {#timelock-controller}

```solidity
contract TimelockController is ITimelockController
```

##### Key Parameters
- Minimum Delay: 4 days
- Proposers: Governance contract
- Executors: Governance Multisig
- Admin: Governance Multisig

##### Core Functions

```solidity
function schedule(
    address target,
    uint256 value,
    bytes calldata data,
    bytes32 predecessor,
    bytes32 salt,
    uint256 delay
) external;

function execute(
    address target,
    uint256 value,
    bytes calldata data,
    bytes32 predecessor,
    bytes32 salt
) external payable;

function cancel(bytes32 id) external;
```

#### Governance Contract {#governance-contract}

The governance contract serves as the primary interface for proposal creation and voting. It implements a time-based voting system with the following carefully balanced parameters:

##### Voting Parameters
- Voting Delay: 1 block (~12 seconds) to ensure proposal visibility
- Voting Period: 3 days to allow sufficient community participation
- Proposal Threshold: 1% of total supply to prevent spam while maintaining accessibility
- Quorum: 4% of total supply to ensure meaningful consensus

#### Key Functions

```solidity
function propose(
    address[] memory targets,
    uint256[] memory values,
    bytes[] memory calldatas,
    string memory description
) external returns (uint256);

function castVote(uint256 proposalId, uint8 support) external returns (uint256);

function castVoteWithReason(
    uint256 proposalId,
    uint8 support,
    string calldata reason
) external returns (uint256);
```

### Governance Actions {#governance-actions}

Governance can execute various system-critical operations, each requiring proper authorization and timelock delays:

#### Rebalance Process {#rebalance-process}

The rebalancing mechanism allows for index composition updates through a structured process:
1. Community proposal of new weights
2. Token holder voting
3. Timelock delay
4. Multisig execution

1. **Submit Bounty**
```solidity
function submitBounty(
    address[] calldata tokens,
    uint256[] calldata nominals,
    uint256 deadline,
    bytes32 root
) external;
```

### Vault Configuration

1. **Update Fee Rate**
```solidity
function setFeeRate(uint256 newRate) external;
```

2. **Update Fee Recipient**
```solidity
function setFeeRecipient(address newRecipient) external;
```

### Role Management

```solidity
function grantRole(bytes32 role, address account) external;
function revokeRole(bytes32 role, address account) external;
```

### Security Measures {#security-measures}

The system implements multiple security layers to protect against various attack vectors:

#### Governance Multisig {#governance-multisig}

A group of trusted entities that provide an additional security layer with:

##### Capabilities
- Execute timelocked transactions after community approval
- Cancel malicious proposals before execution
- Emergency pause functionality for critical situations
- Ability to upgrade contracts through careful deliberation

```solidity
function setEmergency(bool state) external onlyEmergencyMultisig;
```

### Participation Guide {#participation-guide}

A comprehensive guide to engaging with AMKT governance:

#### 1. Token Voting
Users can participate through multiple mechanisms:
- Direct voting on Tally (recommended for most users)
- Delegation to expert voters or DAOs
- Self-custody voting through direct contract interaction

```solidity
function delegate(address delegatee) external;
function delegateBySig(
    address delegatee,
    uint256 nonce,
    uint256 expiry,
    uint8 v,
    bytes32 r,
    bytes32 s
) external;
```

### 2. Proposal Creation
Requirements:
- Must hold or be delegated >= 1% of total supply
- Proposals must be executable actions

### 3. Voting Process
1. Proposal Creation
2. Voting Delay (1 block)
3. Voting Period (3 days)
4. Timelock Delay (4 days)
5. Execution by Multisig

### Events {#governance-events}

```solidity
event ProposalCreated(
    uint256 proposalId,
    address proposer,
    address[] targets,
    uint256[] values,
    bytes[] calldatas,
    string description
);

event VoteCast(
    address indexed voter,
    uint256 proposalId,
    uint8 support,
    uint256 weight,
    string reason
);

event ProposalExecuted(uint256 proposalId);
event ProposalCanceled(uint256 proposalId);
```

## Integration Examples

### Creating a Proposal
```solidity
// Example: Create a proposal to update fee rate
function createFeeUpdateProposal(uint256 newFeeRate) external {
    address[] memory targets = new address[](1);
    targets[0] = address(vault);
    
    uint256[] memory values = new uint256[](1);
    values[0] = 0;
    
    bytes[] memory calldatas = new bytes[](1);
    calldatas[0] = abi.encodeWithSignature("setFeeRate(uint256)", newFeeRate);
    
    string memory description = "Update fee rate proposal";
    
    governance.propose(targets, values, calldatas, description);
}
```

### Voting on a Proposal
```solidity
// Cast a vote
function vote(uint256 proposalId, bool support) external {
    governance.castVote(proposalId, support ? 1 : 0);
}

// Cast a vote with reason
function voteWithReason(uint256 proposalId, bool support, string memory reason) external {
    governance.castVoteWithReason(proposalId, support ? 1 : 0, reason);
}
```

## Error Codes

```solidity
error InsufficientVotingPower();
error ProposalNotActive();
error InvalidProposalState();
error TimelockNotExpired();
error UnauthorizedExecutor();
```

## Events API Documentation

### Overview

AMKT v2's event system provides:
- Real-time state change notifications
- Historical data tracking
- Integration points for external systems
- Audit trail capabilities
- Indexing support for dApps

### Core Events

#### Vault Events

##### TokensDeposited
Emitted when tokens are added to the vault.
```solidity
event TokensDeposited(
    address indexed depositor,
    address[] tokens,
    uint256[] amounts,
    uint256 indexed timestamp
);
```
**Use Cases**:
- Track deposit volumes
- Monitor user activity
- Calculate TVL changes
- Analyze token flows
- Trigger notifications

##### TokensWithdrawn
Emitted during token withdrawal operations.
```solidity
event TokensWithdrawn(
    address indexed withdrawer,
    address[] tokens,
    uint256[] amounts,
    uint256 indexed timestamp
);
```
**Applications**:
- Monitor outflows
- Track user exits
- Calculate redemption rates
- Risk assessment
- Portfolio tracking

#### Rebalance Events

##### RebalanceInitiated
Signals the start of a rebalancing process.
```solidity
event RebalanceInitiated(
    bytes32 indexed bountyHash,
    uint256 indexed deadline,
    address initiator
);
```
**Monitoring Aspects**:
- Track rebalance frequency
- Measure completion times
- Monitor initiator patterns
- Analyze deadline settings
- Gauge market impact

##### RebalanceCompleted
Marks successful completion of rebalancing.
```solidity
event RebalanceCompleted(
    bytes32 indexed bountyHash,
    address indexed fulfiller,
    uint256 executionTime
);
```
**Analysis Points**:
- Execution efficiency
- Fulfiller performance
- Time to completion
- Success rates
- Gas optimization

#### Governance Events

##### ProposalCreated
Emitted when new governance proposals are submitted.
```solidity
event ProposalCreated(
    uint256 indexed proposalId,
    address indexed proposer,
    string description,
    uint256 startBlock,
    uint256 endBlock
);
```
**Governance Tracking**:
- Proposal activity
- Proposer analysis
- Voting periods
- Community engagement
- Governance trends

##### ProposalExecuted
Signals successful proposal execution.
```solidity
event ProposalExecuted(
    uint256 indexed proposalId,
    address indexed executor,
    uint256 timestamp
);
```
**Implementation Metrics**:
- Execution success rates
- Time to execution
- Executor patterns
- Governance efficiency
- Impact analysis

### Emergency Events

##### EmergencyPaused
Indicates activation of emergency pause.
```solidity
event EmergencyPaused(
    address indexed triggeredBy,
    uint256 timestamp,
    string reason
);
```
**Security Monitoring**:
- Incident tracking
- Response times
- Risk patterns
- System health
- Recovery planning

##### EmergencyUnpaused
Signals system resumption after pause.
```solidity
event EmergencyUnpaused(
    address indexed triggeredBy,
    uint256 timestamp
);
```
**Recovery Metrics**:
- Downtime duration
- Resolution speed
- System stability
- Recovery patterns
- Impact assessment

### Usage Examples

#### Listening for Events (Web3.js)
```typescript
// Listen for deposit events
vault.events.TokensDeposited({
    fromBlock: 'latest'
})
.on('data', async (event) => {
    const {
        depositor,
        tokens,
        amounts,
        timestamp
    } = event.returnValues;
    
    // Handle deposit event
    console.log(`New deposit from ${depositor}`);
});
```

#### Querying Historical Events (Ethers.js)
```typescript
async function getRebalanceHistory(fromBlock: number, toBlock: number) {
    const filter = vault.filters.RebalanceCompleted();
    const events = await vault.queryFilter(filter, fromBlock, toBlock);
    
    return events.map(event => ({
        bountyHash: event.args.bountyHash,
        fulfiller: event.args.fulfiller,
        executionTime: event.args.executionTime
    }));
}
```

### Event Monitoring Best Practices

1. **Error Handling**
```typescript
vault.events.TokensDeposited()
    .on('error', (error) => {
        console.error('Event error:', error);
        // Implement retry logic
    })
    .on('connected', (subscriptionId) => {
        console.log('Subscription successful:', subscriptionId);
    });
```

2. **Event Confirmation**
```typescript
async function waitForEventConfirmation(txHash: string, confirmations: number) {
    const receipt = await web3.eth.getTransactionReceipt(txHash);
    const currentBlock = await web3.eth.getBlockNumber();
    
    return currentBlock - receipt.blockNumber >= confirmations;
}
```

3. **Event Aggregation**
```typescript
interface EventAggregator {
    deposits: {
        [address: string]: {
            tokens: string[];
            amounts: BigNumber[];
            timestamp: number;
        }[];
    };
    rebalances: {
        bountyHash: string;
        fulfiller: string;
        executionTime: number;
    }[];
}
```

### Security Considerations

1. **Event Verification**
   - Signature validation
   - Parameter bounds checking
   - Source contract verification
   - Timestamp validation
   - Data integrity checks

2. **Reorg Handling**
   - Chain reorganization detection
   - Event confirmation tracking
   - State reconciliation
   - Data consistency
   - Recovery procedures

3. **Rate Limiting**
   - Request throttling
   - Caching strategies
   - Batch processing
   - Resource management
   - Performance optimization

## Error Codes Documentation

### Overview

AMKT v2 uses custom errors for gas-efficient error handling and better developer experience. This document outlines all error codes, their meanings, and how to handle them.

### Core Errors

#### Vault Errors

##### InsufficientBalance
Thrown when an operation requires more tokens than available.
```solidity
error InsufficientBalance(
    address token,
    uint256 required,
    uint256 available
);
```
**Common Scenarios**:
- Withdrawal exceeds balance
- Transfer amount too high
- Redemption insufficient tokens
- Staking more than owned
- Fee payment shortfall

##### InvalidToken
Thrown when attempting to interact with an unsupported token.
```solidity
error InvalidToken(
    address token
);
```
**Typical Cases**:
- Non-whitelisted token
- Zero address provided
- Deprecated token version
- Blacklisted address
- Non-compliant ERC20

##### MaxCapacityExceeded
Thrown when a deposit would exceed the vault's maximum capacity.
```solidity
error MaxCapacityExceeded(
    uint256 attempted,
    uint256 maximum
);
```
**Common Triggers**:
- Large deposits
- Mint operations
- Rebalancing actions
- Batch transfers
- Inflation events

#### Rebalance Errors

##### InvalidBounty
Thrown when a bounty's parameters are invalid.
```solidity
error InvalidBounty(
    bytes32 bountyHash,
    string reason
);
```
**Validation Failures**:
- Invalid signature
- Incorrect parameters
- Malformed data
- Unauthorized source
- Logic conflicts

##### ExpiredBounty
Thrown when attempting to execute an expired bounty.
```solidity
error ExpiredBounty(
    uint256 deadline,
    uint256 currentTime
);
```
**Time-Related Issues**:
- Execution after deadline
- Block timestamp conflicts
- Network delays
- Timing constraints
- Grace period exceeded

##### SlippageExceeded
Thrown when trade slippage exceeds allowed threshold.
```solidity
error SlippageExceeded(
    address token,
    uint256 expected,
    uint256 received
);
```
**Market Conditions**:
- High volatility
- Low liquidity
- Large trade impact
- Price manipulation
- Oracle delays

#### Governance Errors

##### UnauthorizedProposer
Thrown when an address attempts to create a proposal without sufficient voting power.
```solidity
error UnauthorizedProposer(
    address proposer,
    uint256 votingPower,
    uint256 required
);
```
**Authorization Issues**:
- Insufficient tokens
- Delegation problems
- Power threshold
- Lock period
- Voting rights

##### InvalidProposalState
Thrown when attempting an action on a proposal in an invalid state.
```solidity
error InvalidProposalState(
    uint256 proposalId,
    uint8 currentState,
    uint8 requiredState
);
```
**State Conflicts**:
- Premature execution
- Double execution
- Cancelled proposals
- Queued status
- Grace period

### Access Control Errors

##### Unauthorized
Thrown when an operation is attempted by an unauthorized address.
```solidity
error Unauthorized(
    address caller,
    bytes4 functionSelector
);
```
**Permission Issues**:
- Role missing
- Admin functions
- Restricted operations
- Time locks
- Multi-sig requirements

##### InvalidMultisig
Thrown when a multisig operation fails validation.
```solidity
error InvalidMultisig(
    uint256 signaturesProvided,
    uint256 signaturesRequired
);
```
**Signature Problems**:
- Insufficient signatures
- Invalid signers
- Duplicate signatures
- Expired signatures
- Wrong order

### Error Handling Examples

#### Contract Level
```solidity
contract AMKTVault {
    function withdraw(address token, uint256 amount) external {
        if (balances[token][msg.sender] < amount) {
            revert InsufficientBalance({
                token: token,
                required: amount,
                available: balances[token][msg.sender]
            });
        }
        // ... withdrawal logic
    }
}
```

#### Client Level (TypeScript)
```typescript
async function safeWithdraw(token: string, amount: BigNumber) {
    try {
        const tx = await vault.withdraw(token, amount);
        await tx.wait();
    } catch (error) {
        if (error.code === 'InsufficientBalance') {
            console.error(
                `Insufficient balance: Required ${error.args.required}, ` +
                `Available: ${error.args.available}`
            );
            // Handle insufficient balance
        }
        throw error;
    }
}
```

### Error Handling Best Practices

1. **Custom Error Decoding**
```typescript
interface DecodedError {
    name: string;
    args: any[];
}

function decodeError(error: any): DecodedError {
    const iface = new ethers.utils.Interface([
        "error InsufficientBalance(address,uint256,uint256)",
        "error InvalidToken(address)",
        // ... other error definitions
    ]);
    
    try {
        const decoded = iface.parseError(error.data);
        return {
            name: decoded.name,
            args: decoded.args
        };
    } catch {
        return {
            name: "Unknown",
            args: []
        };
    }
}
```

2. **Error Recovery Strategies**
```typescript
async function executeWithRetry(
    operation: () => Promise<any>,
    maxAttempts: number = 3
): Promise<any> {
    let lastError;
    
    for (let i = 0; i < maxAttempts; i++) {
        try {
            return await operation();
        } catch (error) {
            lastError = error;
            if (!isRetryableError(error)) {
                throw error;
            }
            await delay(Math.pow(2, i) * 1000); // Exponential backoff
        }
    }
    
    throw lastError;
}
```

### Error Monitoring

1. **Error Aggregation**
```typescript
interface ErrorMetrics {
    errorCode: string;
    frequency: number;
    lastOccurrence: Date;
    affectedAddresses: string[];
}

class ErrorMonitor {
    private metrics: Map<string, ErrorMetrics>;
    
    logError(error: any, address: string) {
        const decoded = decodeError(error);
        // Update metrics
        // Trigger alerts if necessary
    }
}
```

2. **Alert Thresholds**
```typescript
const ERROR_THRESHOLDS = {
    InsufficientBalance: 10,  // Alert after 10 occurrences
    SlippageExceeded: 5,     // Alert after 5 occurrences
    InvalidMultisig: 1       // Alert immediately
};
```

### Security Implications

1. **Error Information Exposure**
   - Avoid exposing sensitive information in error messages
   - Sanitize error data before logging
   - Use appropriate error visibility levels

2. **Error Recovery**
   - Implement safe fallback states
   - Ensure atomic operations
   - Handle partial failures gracefully

3. **Monitoring and Alerts**
   - Set up real-time error monitoring
   - Define critical error thresholds
   - Implement automated response procedures 

## Testing Documentation {#testing-documentation}

### Overview {#testing-overview}

AMKT v2 employs a comprehensive testing strategy using two complementary frameworks:
- **Foundry**: For unit and integration testing, providing rapid feedback and high test coverage
- **Halmos**: For symbolic testing and formal verification, ensuring mathematical correctness of critical operations

Each framework serves distinct but complementary purposes in ensuring system reliability.

### Foundry Tests {#foundry-tests}

#### Setup
Standard installation and test execution:
```bash
# Install Foundry - development framework for robust testing
curl -L https://foundry.paradigm.xyz | bash
foundryup

# Run tests from the contracts directory
cd contracts
forge test
```

#### Test Structure
Tests are organized hierarchically for maintainability and clarity:
```solidity:contracts/test/
// Base test setup - provides common testing infrastructure
abstract contract TestBase {
    Vault vault;              // Core vault contract
    Issuance issuance;        // Token issuance management
    InvokeableBounty bounty;  // Rebalancing mechanism
    // ... other common test setup
}

// Test categories - organized by major system components
contract VaultTest is TestBase {
    function testVirtualUnits() public { /* Tests unit calculation logic */ }
    function testInvokeERC20s() public { /* Tests token interaction safety */ }
    // ... other vault tests
}

contract IssuanceTest is TestBase {
    function testIssue() public { /* Tests token minting process */ }
    function testRedeem() public { /* Tests redemption mechanics */ }
    // ... other issuance tests
}
```

#### Key Test Commands
Essential commands for different testing scenarios:
```bash
# Run all tests - comprehensive system verification
forge test

# Run specific test file - focused component testing
forge test --match-path test/Vault.t.sol

# Run tests with specific pattern - targeted feature testing
forge test --match-test "testIssue"

# Run tests with gas reporting - optimization analysis
forge test --gas-report

# Run tests with verbosity - detailed debugging output
forge test -vvv
```

### Symbolic Testing with Halmos {#symbolic-testing}

#### Setup
Installation and execution of formal verification tools:
```bash
# Install Halmos for symbolic testing
cargo install halmos

# Run symbolic tests from contracts directory
cd contracts
halmos
```

#### Key Test Areas

##### 1. State Invariants
Critical system properties that must always hold:
```solidity
// Example invariant test - validates core economic properties
function invariant_totalSupply() public {
    assertEq(
        token.totalSupply(),
        initialSupply + minted - burned,
        "Supply invariant violated"
    );
}
```

##### 2. Access Control
Verification of permission systems:
```solidity
// Example access control test - ensures proper authorization
function verify_onlyAuthorized(address caller) public {
    vm.assume(caller != authorized);
    vm.expectRevert("Unauthorized");
    vault.invokeERC20s(caller);
}
```

### Test Coverage {#test-coverage}

Comprehensive coverage reporting tools:
```bash
# Generate basic test coverage report
forge coverage

# Generate detailed HTML coverage report for analysis
forge coverage --report lcov
genhtml lcov.info -o coverage
```

### Testing Best Practices {#testing-best-practices}

#### 1. Test Organization
Structured approach to test case design:
```solidity
contract ContractTest {
    // Setup - initialize test environment
    function setUp() public { /* Configure test state */ }

    // Happy path tests - verify expected behavior
    function test_StandardOperation() public { /* Test normal flow */ }

    // Edge cases - verify boundary conditions
    function test_EdgeCase() public { /* Test extreme scenarios */ }

    // Failure cases - verify proper error handling
    function testFail_InvalidInput() public { /* Test error conditions */ }

    // Fuzz tests - verify behavior across random inputs
    function testFuzz_Operation(uint256 amount) public { /* Property-based testing */ }
}
```

#### 2. Common Test Scenarios {#test-scenarios}

##### Vault Testing
Comprehensive vault functionality verification:
```solidity
function test_VaultOperations() public {
    // Test asset deposits - verify safe token handling
    // Test withdrawals - ensure proper balance accounting
    // Test fee calculations - validate economic model
    // Test emergency procedures - verify safety mechanisms
}
```

##### Issuance Testing
Token lifecycle verification:
```solidity
function test_IssuanceFlow() public {
    // Test token minting - verify supply management
    // Test redemptions - ensure proper value exchange
    // Test slippage protection - verify price safety
}
```

##### Bounty Testing
Rebalancing mechanism verification:
```solidity
function test_BountyExecution() public {
    // Test bounty validation - verify proof checking
    // Test rebalancing logic - ensure weight compliance
    // Test deadline enforcement - verify temporal constraints
}
```

#### 3. Fuzzing Parameters {#fuzzing-parameters}
Advanced property-based testing configuration:
```bash
# Run fuzz tests with enhanced parameters
forge test --fuzz-runs 10000 --fuzz-max-local-rejects 100000
```

## CI/CD Integration and Testing Guide

### CI/CD Integration
Continuous Integration and Continuous Deployment (CI/CD) is crucial for maintaining code quality and ensuring reliable deployments.

#### GitHub Actions Workflow
This workflow runs on every push and pull request to catch issues early. Key components:
- Uses Ubuntu for consistent testing environment
- Installs Foundry for Solidity testing
- Runs both standard tests and formal verification (Halmos)

```yaml
name: Tests
on: [push, pull_request]

jobs:
  tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Install Foundry
        uses: foundry-rs/foundry-toolchain@v1
      - name: Run tests
        run: |
          cd contracts
          forge test
      - name: Run Halmos
        run: |
          cd contracts
          halmos
```

### Debugging Tools
Effective debugging is essential for smart contract development. These tools help identify and resolve issues quickly.

#### Trace Logging
Verbose logging helps track contract execution flow:
- `-v`: Shows basic test logs
- `-vv`: Adds gas reports
- `-vvv`: Includes stack traces
- `-vvvv`: Provides full debug output with step-by-step execution

```bash
## Run tests with detailed traces
forge test -vvvv
```

#### Gas Snapshots
Gas monitoring is crucial for optimization:
- Regular snapshots help track gas changes
- Useful for identifying expensive operations
- Helps maintain consistent gas costs
- Essential for upgrade planning

```bash
## Create gas snapshot
forge snapshot

## Compare gas usage
forge snapshot --check
```

#### Debug Console
Console logging during tests provides insights into:
- Variable values at specific execution points
- Address interactions
- State changes
- Event emissions
- Error conditions

```solidity
function test_Debug() public {
    console.log("Debug value:", someValue);
    console.logAddress(address);
    console.logUint(amount);
}
```

### Common Issues and Solutions
Understanding common problems and their solutions speeds up development.

#### Gas Estimation Failures
Gas issues often occur due to:
- Complex operations
- Large state changes
- Array operations
- External contract calls
- Loop iterations

```bash
## Run with higher gas limit
forge test --gas-limit 9000000
```

#### Memory Issues
Memory management is critical:
- Large data structures
- String operations
- Array manipulations
- Contract deployment size
- Test suite complexity

```bash
## Adjust memory limits
forge test --memory-limit 4096
```

#### Stack Too Deep
Stack depth issues typically arise from:
- Too many local variables
- Complex function logic
- Nested function calls
- Large struct returns
- Multiple memory operations

```solidity
// Break down complex tests into smaller functions
function helperFunction() internal returns (uint256) {
    // Complex logic here
}
```

#### Additional Best Practices

1. **Test Organization**
   - Group related tests
   - Use descriptive names
   - Maintain test independence
   - Mock external dependencies
   - Test edge cases

2. **CI Pipeline Optimization**
   - Cache dependencies
   - Parallel test execution
   - Selective testing
   - Regular cleanup
   - Resource management

3. **Monitoring and Alerts**
   - Test failure notifications
   - Gas usage warnings
   - Coverage reports
   - Build time tracking
   - Environment status

## Security Considerations

### Overview

AMKT v2 implements multiple layers of security measures to protect user funds and ensure system integrity. The security architecture follows defense-in-depth principles:
- Multiple independent authorization layers
- Time-delayed operations for critical changes
- Economic security through slippage controls
- Automated monitoring and alerting systems
- Emergency response capabilities

### Access Control

#### Role-Based Permissions
The system implements granular role-based access control (RBAC) to ensure precise permission management:

##### Core Roles
Each role serves a specific purpose with carefully scoped permissions:
```solidity
enum Role {
    ADMIN,      // System-wide configuration changes
    OPERATOR,   // Day-to-day operations
    FULFILLER,  // Rebalance execution
    EMERGENCY_MULTISIG,  // Emergency interventions
    GUARDIAN    // Circuit breaker control
}
```

##### Implementation Example
Secure role management with strict access controls:
```solidity
contract AccessControl {
    mapping(address => mapping(Role => bool)) private roles;
    
    modifier onlyRole(Role role) {
        require(roles[msg.sender][role], "Unauthorized");
        _;
    }
    
    function grantRole(address account, Role role) 
        external 
        onlyRole(Role.ADMIN) 
    {
        roles[account][role] = true;
    }
}
```

#### Time Locks

##### Governance Delay
Enforces mandatory waiting periods for critical operations:
```solidity
contract TimelockController {
    uint256 public constant MIN_DELAY = 2 days;    // Minimum safety window
    uint256 public constant MAX_DELAY = 30 days;   // Maximum delay to prevent deadlocks
    
    mapping(bytes32 => uint256) public operationTimestamps;
    
    function scheduleOperation(bytes32 operationId) external {
        operationTimestamps[operationId] = block.timestamp + delay;
    }
    
    function executeOperation(bytes32 operationId) external {
        require(
            block.timestamp >= operationTimestamps[operationId],
            "Operation not ready"
        );
        // Execute operation
    }
}
```

### Value Protection

#### Slippage Control
Protects against adverse price movements during trades:
```solidity
contract SlippageProtection {
    uint256 public constant MAX_SLIPPAGE_BPS = 100; // 1% maximum allowed slippage
    
    function validateSlippage(
        uint256 expected,
        uint256 actual
    ) internal pure {
        uint256 slippage = ((expected - actual) * 10000) / expected;
        require(slippage <= MAX_SLIPPAGE_BPS, "Excessive slippage");
    }
}
```

#### Circuit Breakers
Automatically halts operations when suspicious activity is detected:
```solidity
contract CircuitBreaker {
    uint256 public constant TRADE_VOLUME_THRESHOLD = 1000000e18; // $1M threshold
    uint256 public constant COOL_DOWN_PERIOD = 1 hours;
    
    uint256 public lastBreakTime;
    uint256 public currentVolume;
    
    function checkAndBreak(uint256 tradeAmount) internal {
        currentVolume += tradeAmount;
        
        if (currentVolume >= TRADE_VOLUME_THRESHOLD) {
            lastBreakTime = block.timestamp;
            emit CircuitBroken(currentVolume);
            revert("Circuit breaker triggered");
        }
    }
}
```

### Smart Contract Security

#### Reentrancy Protection
Guards against recursive call attacks:
```solidity
contract ReentrancyGuard {
    uint256 private constant _NOT_ENTERED = 1;
    uint256 private constant _ENTERED = 2;
    uint256 private _status;
    
    modifier nonReentrant() {
        require(_status != _ENTERED, "Reentrant call");
        _status = _ENTERED;
        _;
        _status = _NOT_ENTERED;
    }
}
```

#### Secure Token Transfers
Ensures safe token operations with comprehensive checks:
```solidity
contract SecureTransfers {
    function safeTransfer(
        IERC20 token,
        address recipient,
        uint256 amount
    ) internal {
        require(recipient != address(0), "Invalid recipient");
        
        uint256 balanceBefore = token.balanceOf(address(this));
        bool success = token.transfer(recipient, amount);
        require(success, "Transfer failed");
        
        uint256 balanceAfter = token.balanceOf(address(this));
        require(
            balanceBefore - balanceAfter == amount,
            "Transfer amount mismatch"
        );
    }
}
```

### Emergency Procedures

#### Emergency Shutdown

```solidity
contract EmergencyProtocol {
    address public immutable emergencyMultisig;
    bool public frozen;
    
    event EmergencyShutdown(address triggered_by);
    event EmergencyResume(address triggered_by);
    
    modifier notFrozen() {
        require(!frozen, "Protocol is frozen");
        _;
    }
    
    function initiateEmergencyShutdown() 
        external 
        onlyRole(Role.EMERGENCY_MULTISIG) 
    {
        frozen = true;
        emit EmergencyShutdown(msg.sender);
    }
    
    function resumeFromEmergency()
        external
        onlyRole(Role.EMERGENCY_MULTISIG)
    {
        frozen = false;
        emit EmergencyResume(msg.sender);
    }
}
```

#### Fund Recovery

```solidity
contract FundRecovery {
    function recoverTokens(
        address token,
        address recipient,
        uint256 amount
    ) external onlyRole(Role.EMERGENCY_MULTISIG) {
        require(
            recipient == emergencyMultisig,
            "Can only recover to emergency multisig"
        );
        IERC20(token).safeTransfer(recipient, amount);
        emit TokensRecovered(token, recipient, amount);
    }
}
```

### Monitoring and Alerts

#### Critical Events

```solidity
interface ISecurityMonitor {
    event LargeTransfer(
        address indexed token,
        address indexed from,
        address indexed to,
        uint256 amount
    );
    
    event SlippageWarning(
        address indexed token,
        uint256 expected,
        uint256 actual
    );
    
    event UnauthorizedAttempt(
        address indexed caller,
        bytes4 functionSelector
    );
}
```

#### Monitoring Implementation

```typescript
class SecurityMonitor {
    private readonly LARGE_TRANSFER_THRESHOLD = ethers.utils.parseEther("100000");
    
    async monitorTransfers(transfer: TransferEvent) {
        if (transfer.amount.gt(this.LARGE_TRANSFER_THRESHOLD)) {
            await this.alertLargeTransfer(transfer);
        }
    }
    
    private async alertLargeTransfer(transfer: TransferEvent) {
        // Send alerts to various channels
        await Promise.all([
            this.sendSlackAlert(transfer),
            this.sendEmailAlert(transfer),
            this.triggerPagerDuty(transfer)
        ]);
    }
}
```

### Security Best Practices

1. **Code Review Process**
   - Multiple independent auditors
   - Formal verification for critical components
   - Regular security assessments

2. **Testing Requirements**
   - 100% test coverage for critical functions
   - Fuzz testing for edge cases
   - Integration tests with mainnet forking

3. **Deployment Procedure**
   - Multi-signature deployment
   - Gradual rollout strategy
   - Emergency response team on standby

4. **Ongoing Maintenance**
   - Regular security updates
   - Vulnerability disclosure program
   - Incident response planning 

## Security Considerations

### Overview

AMKT v2 implements multiple layers of security measures to protect user funds and ensure system integrity. The security architecture follows defense-in-depth principles:
- Multiple independent authorization layers
- Time-delayed operations for critical changes
- Economic security through slippage controls
- Automated monitoring and alerting systems
- Emergency response capabilities

### Access Control

#### Role-Based Permissions
The system implements granular role-based access control (RBAC) to ensure precise permission management:

##### Core Roles
Each role serves a specific purpose with carefully scoped permissions:
```solidity
enum Role {
    ADMIN,      // System-wide configuration changes
    OPERATOR,   // Day-to-day operations
    FULFILLER,  // Rebalance execution
    EMERGENCY_MULTISIG,  // Emergency interventions
    GUARDIAN    // Circuit breaker control
}
```

##### Implementation Example
Secure role management with strict access controls:
```solidity
contract AccessControl {
    mapping(address => mapping(Role => bool)) private roles;
    
    modifier onlyRole(Role role) {
        require(roles[msg.sender][role], "Unauthorized");
        _;
    }
    
    function grantRole(address account, Role role) 
        external 
        onlyRole(Role.ADMIN) 
    {
        roles[account][role] = true;
    }
}
```

#### Time Locks

##### Governance Delay
Enforces mandatory waiting periods for critical operations:
```solidity
contract TimelockController {
    uint256 public constant MIN_DELAY = 2 days;    // Minimum safety window
    uint256 public constant MAX_DELAY = 30 days;   // Maximum delay to prevent deadlocks
    
    mapping(bytes32 => uint256) public operationTimestamps;
    
    function scheduleOperation(bytes32 operationId) external {
        operationTimestamps[operationId] = block.timestamp + delay;
    }
    
    function executeOperation(bytes32 operationId) external {
        require(
            block.timestamp >= operationTimestamps[operationId],
            "Operation not ready"
        );
        // Execute operation
    }
}
```

### Value Protection

#### Slippage Control
Protects against adverse price movements during trades:
```solidity
contract SlippageProtection {
    uint256 public constant MAX_SLIPPAGE_BPS = 100; // 1% maximum allowed slippage
    
    function validateSlippage(
        uint256 expected,
        uint256 actual
    ) internal pure {
        uint256 slippage = ((expected - actual) * 10000) / expected;
        require(slippage <= MAX_SLIPPAGE_BPS, "Excessive slippage");
    }
}
```

#### Circuit Breakers
Automatically halts operations when suspicious activity is detected:
```solidity
contract CircuitBreaker {
    uint256 public constant TRADE_VOLUME_THRESHOLD = 1000000e18; // $1M threshold
    uint256 public constant COOL_DOWN_PERIOD = 1 hours;
    
    uint256 public lastBreakTime;
    uint256 public currentVolume;
    
    function checkAndBreak(uint256 tradeAmount) internal {
        currentVolume += tradeAmount;
        
        if (currentVolume >= TRADE_VOLUME_THRESHOLD) {
            lastBreakTime = block.timestamp;
            emit CircuitBroken(currentVolume);
            revert("Circuit breaker triggered");
        }
    }
}
```

### Smart Contract Security

#### Reentrancy Protection
Guards against recursive call attacks:
```solidity
contract ReentrancyGuard {
    uint256 private constant _NOT_ENTERED = 1;
    uint256 private constant _ENTERED = 2;
    uint256 private _status;
    
    modifier nonReentrant() {
        require(_status != _ENTERED, "Reentrant call");
        _status = _ENTERED;
        _;
        _status = _NOT_ENTERED;
    }
}
```

#### Secure Token Transfers
Ensures safe token operations with comprehensive checks:
```solidity
contract SecureTransfers {
    function safeTransfer(
        IERC20 token,
        address recipient,
        uint256 amount
    ) internal {
        require(recipient != address(0), "Invalid recipient");
        
        uint256 balanceBefore = token.balanceOf(address(this));
        bool success = token.transfer(recipient, amount);
        require(success, "Transfer failed");
        
        uint256 balanceAfter = token.balanceOf(address(this));
        require(
            balanceBefore - balanceAfter == amount,
            "Transfer amount mismatch"
        );
    }
}
```

### Emergency Procedures

#### Emergency Shutdown

```solidity
contract EmergencyProtocol {
    address public immutable emergencyMultisig;
    bool public frozen;
    
    event EmergencyShutdown(address triggered_by);
    event EmergencyResume(address triggered_by);
    
    modifier notFrozen() {
        require(!frozen, "Protocol is frozen");
        _;
    }
    
    function initiateEmergencyShutdown() 
        external 
        onlyRole(Role.EMERGENCY_MULTISIG) 
    {
        frozen = true;
        emit EmergencyShutdown(msg.sender);
    }
    
    function resumeFromEmergency()
        external
        onlyRole(Role.EMERGENCY_MULTISIG)
    {
        frozen = false;
        emit EmergencyResume(msg.sender);
    }
}
```

#### Fund Recovery

```solidity
contract FundRecovery {
    function recoverTokens(
        address token,
        address recipient,
        uint256 amount
    ) external onlyRole(Role.EMERGENCY_MULTISIG) {
        require(
            recipient == emergencyMultisig,
            "Can only recover to emergency multisig"
        );
        IERC20(token).safeTransfer(recipient, amount);
        emit TokensRecovered(token, recipient, amount);
    }
}
```

### Monitoring and Alerts

#### Critical Events

```solidity
interface ISecurityMonitor {
    event LargeTransfer(
        address indexed token,
        address indexed from,
        address indexed to,
        uint256 amount
    );
    
    event SlippageWarning(
        address indexed token,
        uint256 expected,
        uint256 actual
    );
    
    event UnauthorizedAttempt(
        address indexed caller,
        bytes4 functionSelector
    );
}
```

#### Monitoring Implementation

```typescript
class SecurityMonitor {
    private readonly LARGE_TRANSFER_THRESHOLD = ethers.utils.parseEther("100000");
    
    async monitorTransfers(transfer: TransferEvent) {
        if (transfer.amount.gt(this.LARGE_TRANSFER_THRESHOLD)) {
            await this.alertLargeTransfer(transfer);
        }
    }
    
    private async alertLargeTransfer(transfer: TransferEvent) {
        // Send alerts to various channels
        await Promise.all([
            this.sendSlackAlert(transfer),
            this.sendEmailAlert(transfer),
            this.triggerPagerDuty(transfer)
        ]);
    }
}
```

### Security Best Practices

1. **Code Review Process**
   - Multiple independent auditors
   - Formal verification for critical components
   - Regular security assessments

2. **Testing Requirements**
   - 100% test coverage for critical functions
   - Fuzz testing for edge cases
   - Integration tests with mainnet forking

3. **Deployment Procedure**
   - Multi-signature deployment
   - Gradual rollout strategy
   - Emergency response team on standby

4. **Ongoing Maintenance**
   - Regular security updates
   - Vulnerability disclosure program
   - Incident response planning 

## Best Practices

### Overview

This document outlines the recommended best practices for developing, deploying, and maintaining AMKT v2 components. Following these guidelines helps ensure code quality, security, and maintainability.

### Code Development

#### Solidity Patterns

##### Safe Math Operations
While Solidity 0.8.x includes built-in overflow checks, using SafeMath remains a best practice for:
- Explicit intention documentation
- Compatibility with older code
- Additional safety features beyond basic overflow protection
- Consistent arithmetic handling across different Solidity versions

```solidity
contract SafeMathExample {
    using SafeMath for uint256;
    
    function calculateShare(uint256 amount, uint256 total) public pure returns (uint256) {
        // Always use SafeMath for arithmetic operations
        return amount.mul(10000).div(total);
    }
}
```

##### Check-Effects-Interactions Pattern
This pattern is crucial for preventing reentrancy attacks. The order is important because:
1. **Checks**: Validate all conditions first to fail fast and save gas
2. **Effects**: Update internal state while still holding the transaction context
3. **Interactions**: External calls go last to prevent malicious contract callbacks

```solidity
contract CEIPattern {
    mapping(address => uint256) private balances;
    
    function withdraw(uint256 amount) external {
        // 1. Checks
        require(balances[msg.sender] >= amount, "Insufficient balance");
        
        // 2. Effects
        balances[msg.sender] = balances[msg.sender].sub(amount);
        
        // 3. Interactions
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success, "Transfer failed");
    }
}
```

#### Code Organization

##### Module Structure
Modular design provides several benefits:
- **Separation of Concerns**: Each module handles one specific aspect
- **Upgradability**: Modules can be upgraded independently
- **Testing**: Easier to test isolated functionality
- **Access Control**: Granular permissions per module
- **Gas Optimization**: Users only interact with needed modules

```solidity
// Core functionality
contract AMKTCore {
    // Essential state variables and functions
}

// Additional features as separate modules
contract AMKTRebalancer is AMKTCore {
    // Rebalancing specific logic
}

contract AMKTGovernance is AMKTCore {
    // Governance specific logic
}
```

### Testing Guidelines

#### Unit Testing
Best practices for unit tests include:
- One assertion per test when possible
- Descriptive test names that indicate behavior
- Setup/teardown in beforeEach/afterEach
- Testing both success and failure cases
- Mock external dependencies

```typescript
describe("AMKT Vault", () => {
    let vault: AMKTVault;
    let token: MockToken;
    
    beforeEach(async () => {
        // Setup for each test
        vault = await deployVault();
        token = await deployMockToken();
    });
    
    it("should handle deposits correctly", async () => {
        // Arrange
        const amount = ethers.utils.parseEther("100");
        
        // Act
        await vault.deposit(token.address, amount);
        
        // Assert
        expect(await vault.balanceOf(token.address))
            .to.equal(amount);
    });
});
```

#### Integration Testing
Fork testing is essential because:
- Tests real protocol interactions
- Catches integration issues early
- Validates assumptions about external protocols
- Ensures compatibility with mainnet state
- Helps estimate real gas costs

```typescript
describe("Integration Tests", () => {
    before(async () => {
        await network.provider.request({
            method: "hardhat_reset",
            params: [{
                forking: {
                    jsonRpcUrl: process.env.MAINNET_RPC,
                    blockNumber: 15000000
                }
            }]
        });
    });
    
    it("should interact with real DeFi protocols", async () => {
        // Test interactions with actual mainnet contracts
    });
});
```

### Documentation Standards

#### Code Documentation
High-quality documentation should:
- Explain the "why" not just the "what"
- Include security considerations
- Document all parameters and return values
- Specify units (e.g., wei vs ether)
- Note any important state requirements

```solidity
/// @notice Deposits tokens into the vault
/// @dev Requires approval for token transfer
/// @param token The address of the token to deposit
/// @param amount The amount of tokens to deposit
/// @return shares The number of shares minted
function deposit(
    address token,
    uint256 amount
) external returns (uint256 shares) {
    // Implementation
}
```

##### Event Documentation
```solidity
/// @notice Emitted when tokens are deposited
/// @param user The address that deposited tokens
/// @param token The token that was deposited
/// @param amount The amount that was deposited
/// @param shares The number of shares minted
event Deposit(
    address indexed user,
    address indexed token,
    uint256 amount,
    uint256 shares
);
```

### Deployment Process

#### Deployment Checklist
A thorough deployment process should include:
- Multiple environment testing (testnet → staging → mainnet)
- Gas optimization verification
- Admin key security setup
- Emergency procedures documentation
- Monitoring system configuration

```typescript
interface DeploymentChecklist {
    readonly steps: {
        readonly preDeployment: [
            "Verify contract bytecode",
            "Check constructor parameters",
            "Confirm gas estimates"
        ];
        readonly deployment: [
            "Deploy implementation contracts",
            "Initialize proxy contracts",
            "Set initial parameters"
        ];
        readonly postDeployment: [
            "Verify contract addresses",
            "Transfer admin rights",
            "Enable functionality gradually"
        ];
    };
}
```

#### Configuration Management

```typescript
interface DeployConfig {
    readonly network: {
        chainId: number;
        name: string;
    };
    readonly contracts: {
        [key: string]: {
            address: string;
            params: any[];
        };
    };
    readonly timelock: {
        minDelay: number;
        proposers: string[];
        executors: string[];
    };
}
```

### Monitoring and Maintenance

#### Health Checks
Comprehensive monitoring should track:
- Contract state consistency
- Balance reconciliation
- Oracle data freshness
- Gas price trends
- Network conditions
- User operation patterns

```typescript
class HealthMonitor {
    async checkSystemHealth(): Promise<HealthStatus> {
        const checks = await Promise.all([
            this.checkContractState(),
            this.checkTokenBalances(),
            this.checkPriceFeeds(),
            this.checkGasPrice()
        ]);
        
        return {
            healthy: checks.every(c => c.healthy),
            details: checks
        };
    }
}
```

#### Performance Optimization
Key optimization strategies:
- Batch operations when possible
- Use events for off-chain tracking
- Optimize storage layout
- Cache frequently accessed values
- Consider view function gas costs

```solidity
contract GasOptimized {
    // Use uint256 instead of smaller uints
    uint256 private constant PRECISION = 1e18;
    
    // Pack related storage variables
    struct UserInfo {
        uint128 balance;
        uint128 lastUpdate;
    }
    
    // Use mappings for O(1) access
    mapping(address => UserInfo) private userInfo;
}
```

### Upgrade Procedures

#### Safe Upgrade Pattern
Important upgrade considerations:
- Storage layout compatibility
- Function selector stability
- State migration planning
- Rollback procedures
- User communication strategy

```solidity
contract UpgradeableVault {
    /// @custom:oz-upgrades-unsafe-allow constructor
    constructor() {
        _disableInitializers();
    }
    
    function initialize(address admin) external initializer {
        __AccessControl_init();
        _grantRole(DEFAULT_ADMIN_ROLE, admin);
    }
    
    function upgradeToAndCall(
        address newImplementation,
        bytes memory data
    ) external onlyRole(UPGRADER_ROLE) {
        _upgradeToAndCall(newImplementation, data);
    }
}
```

### Error Handling

#### Error Recovery
Error handling should:
- Distinguish between recoverable and fatal errors
- Implement circuit breakers where appropriate
- Log detailed error information
- Maintain system invariants
- Have clear escalation paths

```typescript
class ErrorHandler {
    async handleTransactionError(error: any): Promise<void> {
        if (this.isRetryableError(error)) {
            await this.retryTransaction();
        } else if (this.isGasError(error)) {
            await this.adjustGasAndRetry();
        } else {
            await this.notifyAdmins(error);
            throw error;
        }
    }
}
```

### Community Guidelines

Community interaction best practices emphasize:
1. **Code Contributions**
   - Detailed PR descriptions
   - Impact assessment
   - Breaking change notification
   - Benchmark comparisons
   - Security considerations

2. **Issue Reporting**
   - Clear reproduction steps
   - Version information
   - Related issues/PRs
   - Expected vs actual behavior
   - Impact assessment

3. **Communication**
   - Clear technical writing
   - Regular updates
   - Inclusive language
   - Constructive feedback
   - Knowledge sharing