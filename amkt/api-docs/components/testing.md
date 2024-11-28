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
