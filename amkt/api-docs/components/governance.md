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
