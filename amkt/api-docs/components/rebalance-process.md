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

