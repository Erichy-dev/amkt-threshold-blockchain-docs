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