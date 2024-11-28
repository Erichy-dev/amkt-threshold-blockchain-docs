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

