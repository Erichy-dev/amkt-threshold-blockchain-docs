## AMKT v2 Rebalance API Documentation

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

