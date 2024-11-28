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
