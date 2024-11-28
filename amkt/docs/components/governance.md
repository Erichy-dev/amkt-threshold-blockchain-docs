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
