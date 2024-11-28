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