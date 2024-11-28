# Alongside Crypto Market Index v2

Alongside Crypto Market Index (AMKT) is a fully backed market index, providing exposure to a market-cap weighted basket of assets, to be rebalanced quarterly.

The next iteration of AMKT moves custody on-chain, relying on `Vault` to custody underlying assets, and on governance to submit accurate `Bounty` for the next set of underlying assets to rebalance.

## Table of Contents

- [Alongside Crypto Market Index v2](#alongside-crypto-market-index-v2)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
    - [System Relationship Diagram](#system-relationship-diagram)
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
  - [Error Codes](#error-codes-1)
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
  - [AMKT v2 Rebalance](#amkt-v2-rebalance)
    - [Overview](#overview-1)
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
  - [Error Codes](#error-codes-2)
  - [Usage Examples](#usage-examples)
    - [1. Calculate Rebalance Requirements](#1-calculate-rebalance-requirements)
    - [2. Submit Rebalance Proposal](#2-submit-rebalance-proposal)
    - [3. Execute Rebalance](#3-execute-rebalance)
  - [Security Considerations](#security-considerations)
  - [Best Practices](#best-practices)
  - [Events API Documentation](#events-api-documentation)
    - [Overview](#overview-2)
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
    - [Overview](#overview-3)
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
  - [Security Considerations](#security-considerations-2)
    - [Overview](#overview-4)
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

## Overview

### System Relationship Diagram

![Core System Relationship Diagram](https://raw.githubusercontent.com/Alongside-Finance/amkt-v2/main/misc/diagrams/system.png)
_This diagram provides a high-level overview of the system and its core functions._

{!./components/_readme.md!}
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

{!./components/installation.md!}
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

{!./components/contracts.md!}
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

###### Token Operations
```solidity
function invokeMint(address to, uint256 amount) external onlyIssuance
```
- **Description**: Mints AMKT tokens
- **Parameters**:
  - `to`: Recipient address
  - `amount`: Amount to mint
- **Access**: Only Issuance contract

```solidity
function invokeBurn(address from, uint256 amount) external onlyIssuance
```
- **Description**: Burns AMKT tokens
- **Parameters**:
  - `from`: Address to burn from
  - `amount`: Amount to burn
- **Access**: Only Issuance contract

###### Fee Management
```solidity
function tryInflation() external returns (uint256 mintedAmount)
```
- **Description**: Mints accrued fees and adjusts virtualUnits
- **Returns**: Amount of tokens minted
- **Access**: Only fee recipient
- **Events Emitted**: `InflationMinted(uint256 amount)`

###### Emergency Controls
```solidity
function setEmergency(bool state) external onlyEmergencyMultisig
```
- **Description**: Sets emergency state to pause/unpause operations
- **Parameters**:
  - `state`: Boolean emergency state
- **Access**: Only emergency multisig
- **Events Emitted**: `EmergencySet(bool state)`

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

### Events {#events}

#### Vault Events
```solidity
event InflationMinted(uint256 amount);
event EmergencySet(bool state);
event VirtualUnitsSet(address[] tokens, uint256[] amounts);
```

#### Issuance Events
```solidity
event Issue(address indexed recipient, uint256 amount);
event Redeem(address indexed redeemer, uint256 amount);
```

#### Bounty Events
```solidity
event BountyFulfilled(bytes32 indexed bountyHash);
event BountySet(bytes32 indexed bountyHash);
```

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

{!./components/governance.md!}
## Governance API {#governance-api}

### Overview {#governance-overview}

The AMKT governance system enables decentralized control over the index through a combination of token voting, time-locked execution, and multi-sig security measures.

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

##### Voting Parameters
- Voting Delay: 1 block
- Voting Period: 3 days
- Proposal Threshold: 1% of total supply
- Quorum: 4% of total supply

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

#### Rebalance Process {#rebalance-process}

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

#### Governance Multisig {#governance-multisig}

##### Capabilities
- Execute timelocked transactions
- Cancel malicious proposals
- Emergency pause functionality

```solidity
function setEmergency(bool state) external onlyEmergencyMultisig;
```

### Participation Guide {#participation-guide}

#### 1. Token Voting
Users can participate in governance with AMKT tokens through:
- Direct voting on Tally
- Delegation to other addresses

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

{!./components/testing.md!}
## Testing Documentation {#testing-documentation}

### Overview {#testing-overview}

AMKT v2 employs two primary testing frameworks:
- **Foundry**: For unit and integration testing
- **Halmos**: For symbolic testing and formal verification

### Foundry Tests {#foundry-tests}

#### Setup
```bash
# Install Foundry
curl -L https://foundry.paradigm.xyz | bash
foundryup

# Run tests
cd contracts
forge test
```

#### Test Structure
```solidity:contracts/test/
// Base test setup
abstract contract TestBase {
    Vault vault;
    Issuance issuance;
    InvokeableBounty bounty;
    // ... other common test setup
}

// Test categories
contract VaultTest is TestBase {
    function testVirtualUnits() public { /* ... */ }
    function testInvokeERC20s() public { /* ... */ }
    // ... other vault tests
}

contract IssuanceTest is TestBase {
    function testIssue() public { /* ... */ }
    function testRedeem() public { /* ... */ }
    // ... other issuance tests
}
```

#### Key Test Commands
```bash
# Run all tests
forge test

# Run specific test file
forge test --match-path test/Vault.t.sol

# Run tests with specific pattern
forge test --match-test "testIssue"

# Run tests with gas reporting
forge test --gas-report

# Run tests with verbosity
forge test -vvv
```

### Symbolic Testing with Halmos {#symbolic-testing}

#### Setup
```bash
# Install Halmos
cargo install halmos

# Run symbolic tests
cd contracts
halmos
```

#### Key Test Areas

##### 1. State Invariants
```solidity
// Example invariant test
function invariant_totalSupply() public {
    assertEq(
        token.totalSupply(),
        initialSupply + minted - burned
    );
}
```

##### 2. Access Control
```solidity
// Example access control test
function verify_onlyAuthorized(address caller) public {
    vm.assume(caller != authorized);
    vm.expectRevert("Unauthorized");
    vault.invokeERC20s(caller);
}
```

### Test Coverage {#test-coverage}

```bash
# Generate test coverage report
forge coverage

# Generate detailed HTML coverage report
forge coverage --report lcov
genhtml lcov.info -o coverage
```

### Testing Best Practices {#testing-best-practices}

#### 1. Test Organization
```solidity
contract ContractTest {
    // Setup
    function setUp() public { /* ... */ }

    // Happy path tests
    function test_StandardOperation() public { /* ... */ }

    // Edge cases
    function test_EdgeCase() public { /* ... */ }

    // Failure cases
    function testFail_InvalidInput() public { /* ... */ }

    // Fuzz tests
    function testFuzz_Operation(uint256 amount) public { /* ... */ }
}
```

#### 2. Common Test Scenarios {#test-scenarios}

##### Vault Testing
```solidity
function test_VaultOperations() public {
    // Test asset deposits
    // Test withdrawals
    // Test fee calculations
    // Test emergency procedures
}
```

##### Issuance Testing
```solidity
function test_IssuanceFlow() public {
    // Test token minting
    // Test redemptions
    // Test slippage protection
}
```

##### Bounty Testing
```solidity
function test_BountyExecution() public {
    // Test bounty validation
    // Test rebalancing logic
    // Test deadline enforcement
}
```

#### 3. Fuzzing Parameters {#fuzzing-parameters}
```bash
# Run fuzz tests with custom parameters
forge test --fuzz-runs 10000 --fuzz-max-local-rejects 100000
```

{!./components/ci-cd.md!}
## CI/CD Integration and Testing Guide

### CI/CD Integration

#### GitHub Actions Workflow
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

#### Trace Logging
```bash
## Run tests with detailed traces
forge test -vvvv
```

#### Gas Snapshots
```bash
## Create gas snapshot
forge snapshot

## Compare gas usage
forge snapshot --check
```

#### Debug Console
```solidity
function test_Debug() public {
    console.log("Debug value:", someValue);
    console.logAddress(address);
    console.logUint(amount);
}
```

### Common Issues and Solutions

#### Gas Estimation Failures
```bash
## Run with higher gas limit
forge test --gas-limit 9000000
```

#### Memory Issues
```bash
## Adjust memory limits
forge test --memory-limit 4096
```

#### Stack Too Deep
```solidity
// Break down complex tests into smaller functions
function helperFunction() internal returns (uint256) {
    // Complex logic here
}
```

{!./components/rebalance-process.md!}
## AMKT v2 Rebalance

### Overview

The rebalance system allows for periodic updates to the index composition through a secure, governance-controlled process using the SDK and smart contracts.

### Rebalance SDK

#### Installation
```bash
cd sdk/rebalance
cargo build
```

#### Core Components

##### 1. Bounty Structure
```rust
pub struct Bounty {
    tokens: Vec<Address>,    // List of token addresses
    nominals: Vec<u256>,     // New token quantities
    deadline: u256,          // Timestamp for bounty expiration
    root: [u8; 32]          // Merkle root for validation
}
```

#### 2. Rebalance Configuration
```rust
pub struct RebalanceConfig {
    target_weights: HashMap<Address, Decimal>,  // Target portfolio weights
    current_holdings: HashMap<Address, u256>,   // Current token holdings
    price_data: HashMap<Address, Decimal>,      // Current token prices
    slippage_tolerance: Decimal                 // Maximum allowed slippage
}
```

## Smart Contract Interface

### InvokeableBounty Contract

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

### 1. Preparation Phase
```rust
// Example SDK usage for preparing a rebalance
let rebalance = RebalanceCalculator::new(config)
    .calculate_required_trades()
    .generate_bounty();
```

### 2. Governance Proposal
```solidity
// Submit bounty through governance
function proposeBountyUpdate(
    address[] calldata tokens,
    uint256[] calldata nominals,
    uint256 deadline,
    bytes32 root
) external returns (uint256 proposalId)
```

### 3. Execution
```solidity
// After governance approval, fulfiller executes
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
   - Merkle proof verification
   - Deadline enforcement
   - Slippage protection

2. **Access Control**
   - Governance-controlled bounty setting
   - Restricted fulfiller access
   - Timelock delays

3. **Economic Security**
   - Maximum weight changes
   - Price impact limits
   - Token whitelist

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

{!./components/events.md!}
## Events API Documentation

### Overview

AMKT v2 emits various events across its smart contracts to enable tracking of important state changes and actions. These events are essential for indexers, monitoring tools, and frontend applications.

### Core Events

#### Vault Events

##### TokensDeposited
Emitted when tokens are deposited into the vault.
```solidity
event TokensDeposited(
    address indexed depositor,
    address[] tokens,
    uint256[] amounts,
    uint256 indexed timestamp
);
```

##### TokensWithdrawn
Emitted when tokens are withdrawn from the vault.
```solidity
event TokensWithdrawn(
    address indexed withdrawer,
    address[] tokens,
    uint256[] amounts,
    uint256 indexed timestamp
);
```

#### Rebalance Events

##### RebalanceInitiated
Emitted when a rebalance process begins.
```solidity
event RebalanceInitiated(
    bytes32 indexed bountyHash,
    uint256 indexed deadline,
    address initiator
);
```

##### RebalanceCompleted
Emitted when a rebalance is successfully executed.
```solidity
event RebalanceCompleted(
    bytes32 indexed bountyHash,
    address indexed fulfiller,
    uint256 executionTime
);
```

#### Governance Events

##### ProposalCreated
Emitted when a new governance proposal is created.
```solidity
event ProposalCreated(
    uint256 indexed proposalId,
    address indexed proposer,
    string description,
    uint256 startBlock,
    uint256 endBlock
);
```

##### ProposalExecuted
Emitted when a proposal is executed.
```solidity
event ProposalExecuted(
    uint256 indexed proposalId,
    address indexed executor,
    uint256 timestamp
);
```

### Emergency Events

##### EmergencyPaused
Emitted when emergency pause is activated.
```solidity
event EmergencyPaused(
    address indexed triggeredBy,
    uint256 timestamp,
    string reason
);
```

##### EmergencyUnpaused
Emitted when emergency pause is deactivated.
```solidity
event EmergencyUnpaused(
    address indexed triggeredBy,
    uint256 timestamp
);
```

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
   - Always verify event signatures
   - Check event parameters against expected ranges
   - Validate emitting contract addresses

2. **Reorg Handling**
   - Implement reorg detection
   - Store event status (pending/confirmed)
   - Update state on chain reorganizations

3. **Rate Limiting**
   - Implement backoff strategies
   - Cache frequently accessed events
   - Batch event processing

{!./components/errors.md!}
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

##### InvalidToken
Thrown when attempting to interact with an unsupported token.
```solidity
error InvalidToken(
    address token
);
```

##### MaxCapacityExceeded
Thrown when a deposit would exceed the vault's maximum capacity.
```solidity
error MaxCapacityExceeded(
    uint256 attempted,
    uint256 maximum
);
```

#### Rebalance Errors

##### InvalidBounty
Thrown when a bounty's parameters are invalid.
```solidity
error InvalidBounty(
    bytes32 bountyHash,
    string reason
);
```

##### ExpiredBounty
Thrown when attempting to execute an expired bounty.
```solidity
error ExpiredBounty(
    uint256 deadline,
    uint256 currentTime
);
```

##### SlippageExceeded
Thrown when trade slippage exceeds allowed threshold.
```solidity
error SlippageExceeded(
    address token,
    uint256 expected,
    uint256 received
);
```

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

##### InvalidProposalState
Thrown when attempting an action on a proposal in an invalid state.
```solidity
error InvalidProposalState(
    uint256 proposalId,
    uint8 currentState,
    uint8 requiredState
);
```

### Access Control Errors

##### Unauthorized
Thrown when an operation is attempted by an unauthorized address.
```solidity
error Unauthorized(
    address caller,
    bytes4 functionSelector
);
```

##### InvalidMultisig
Thrown when a multisig operation fails validation.
```solidity
error InvalidMultisig(
    uint256 signaturesProvided,
    uint256 signaturesRequired
);
```

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

{!./components/security.md!}
## Security Considerations

### Overview

AMKT v2 implements multiple layers of security measures to protect user funds and ensure system integrity. This document outlines key security considerations, best practices, and implementation guidelines.

### Access Control

#### Role-Based Permissions

##### Core Roles
```solidity
enum Role {
    ADMIN,
    OPERATOR,
    FULFILLER,
    EMERGENCY_MULTISIG,
    GUARDIAN
}
```

##### Implementation Example
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
```solidity
contract TimelockController {
    uint256 public constant MIN_DELAY = 2 days;
    uint256 public constant MAX_DELAY = 30 days;
    
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

```solidity
contract SlippageProtection {
    uint256 public constant MAX_SLIPPAGE_BPS = 100; // 1%
    
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

```solidity
contract CircuitBreaker {
    uint256 public constant TRADE_VOLUME_THRESHOLD = 1000000e18; // $1M
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