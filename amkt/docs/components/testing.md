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
