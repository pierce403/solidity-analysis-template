# Forge - Advanced Testing Framework for Solidity Auditing

Forge is a command-line tool that ships with Foundry for building, testing, fuzzing, and debugging Ethereum applications. It's essential for thorough smart contract auditing through comprehensive testing.

## Installation

### Install Foundry (includes Forge)
```bash
# Install foundryup
curl -L https://foundry.paradigm.xyz | bash

# Install latest forge
foundryup
```

### Verify Installation
```bash
forge --version
cast --version
anvil --version
```

## Project Setup for Auditing

### Initialize New Forge Project
```bash
forge init audit-project
cd audit-project
```

### Add Existing Contracts to Forge
```bash
# In existing project directory
forge init --force
mkdir -p src test
# Copy contract files to src/
# Copy or create test files in test/
```

### Install Dependencies
```bash
# Install common testing libraries
forge install foundry-rs/forge-std
forge install OpenZeppelin/openzeppelin-contracts
forge install Vectorized/solady
```

## Essential Testing Commands for Auditing

### 1. Run All Tests
```bash
forge test
```

### 2. Run Tests with Detailed Output
```bash
forge test -vvv
```

### 3. Test Specific Contract or Function
```bash
# Test specific contract
forge test --match-contract TestContract

# Test specific function
forge test --match-test testSpecificFunction
```

### 4. Fork Testing (Essential for DeFi Audits)
```bash
# Test against mainnet fork
forge test --fork-url https://eth-mainnet.alchemyapi.io/v2/YOUR-API-KEY

# Test at specific block
forge test --fork-url $ETH_RPC_URL --fork-block-number 18500000
```

### 5. Gas Analysis
```bash
# Generate gas report
forge test --gas-report

# Save gas report to file
forge test --gas-report > gas-report.txt
```

## Advanced Auditing Techniques

### Property-Based Testing (Fuzzing)
```solidity
// test/FuzzTest.t.sol
contract FuzzTest is Test {
    MyContract target;
    
    function setUp() public {
        target = new MyContract();
    }
    
    // Fuzz test for arithmetic operations
    function testFuzzArithmetic(uint256 a, uint256 b) public {
        vm.assume(a > 0 && b > 0);
        vm.assume(a < type(uint128).max && b < type(uint128).max);
        
        uint256 result = target.add(a, b);
        assertEq(result, a + b);
    }
    
    // Fuzz test for access control
    function testFuzzUnauthorizedAccess(address caller) public {
        vm.assume(caller != target.owner());
        
        vm.prank(caller);
        vm.expectRevert("Unauthorized");
        target.adminFunction();
    }
}
```

### Invariant Testing
```solidity
// test/Invariant.t.sol
contract InvariantTest is Test {
    MyToken token;
    
    function setUp() public {
        token = new MyToken();
        targetContract(address(token));
    }
    
    // Total supply should never change unexpectedly
    function invariant_TotalSupplyConsistent() public {
        assertEq(token.totalSupply(), 1000000e18);
    }
    
    // Balance sum should equal total supply
    function invariant_BalanceSum() public {
        // Implementation depends on tracking all holders
        uint256 sum = 0;
        for (uint i = 0; i < holders.length; i++) {
            sum += token.balanceOf(holders[i]);
        }
        assertEq(sum, token.totalSupply());
    }
}
```

### Reentrancy Testing
```solidity
// test/ReentrancyTest.t.sol
contract ReentrancyAttacker {
    VulnerableContract target;
    uint256 public callCount;
    
    constructor(address _target) {
        target = VulnerableContract(_target);
    }
    
    function attack() external payable {
        target.deposit{value: msg.value}();
        target.withdraw();
    }
    
    receive() external payable {
        callCount++;
        if (callCount < 3 && address(target).balance > 0) {
            target.withdraw();
        }
    }
}

contract ReentrancyTest is Test {
    VulnerableContract target;
    ReentrancyAttacker attacker;
    
    function setUp() public {
        target = new VulnerableContract();
        attacker = new ReentrancyAttacker(address(target));
        
        // Setup initial state
        vm.deal(address(this), 10 ether);
        vm.deal(address(attacker), 1 ether);
        target.deposit{value: 5 ether}();
    }
    
    function testReentrancyAttack() public {
        uint256 initialBalance = address(attacker).balance;
        uint256 contractBalance = address(target).balance;
        
        vm.prank(address(attacker));
        attacker.attack{value: 1 ether}();
        
        // Check if attack was successful
        assertTrue(address(attacker).balance > initialBalance + contractBalance);
    }
}
```

## Forge Configuration for Auditing

### foundry.toml Configuration
```toml
[profile.default]
src = "src"
out = "out"
libs = ["lib"]
optimizer = true
optimizer_runs = 200
via_ir = false
verbosity = 2

# Fuzzing configuration
fuzz = { runs = 10000, max_test_rejects = 65536 }
invariant = { runs = 500, depth = 500, fail_on_revert = false }

# Gas configuration
gas_limit = 30000000
gas_price = 0

# Fork testing
eth_rpc_url = "${ETH_RPC_URL}"

[profile.ci]
fuzz = { runs = 50000 }
invariant = { runs = 1000, depth = 1000 }

[profile.audit]
optimizer = false
fuzz = { runs = 100000 }
invariant = { runs = 2000, depth = 2000 }
```

## Essential Forge Cheatcodes for Auditing

### Time Manipulation
```solidity
// Advance blockchain time
vm.warp(block.timestamp + 30 days);

// Set specific timestamp
vm.warp(1672531200); // January 1, 2023
```

### Access Control Testing
```solidity
// Test as different users
vm.prank(alice);
contract.userFunction();

// Start persistent prank
vm.startPrank(admin);
contract.adminFunction1();
contract.adminFunction2();
vm.stopPrank();
```

### State Manipulation
```solidity
// Set contract storage directly
vm.store(contractAddress, slot, value);

// Set ETH balance
vm.deal(address(user), 100 ether);

// Set token balance (if storage layout is known)
vm.store(tokenAddress, keccak256(abi.encode(user, 0)), 1000e18);
```

### Event Testing
```solidity
// Expect specific event emission
vm.expectEmit(true, true, false, true);
emit Transfer(alice, bob, 100);
token.transfer(bob, 100);
```

### Revert Testing
```solidity
// Expect revert with specific message
vm.expectRevert("Insufficient balance");
token.transfer(bob, 1000);

// Expect revert with custom error
vm.expectRevert(InsufficientBalance.selector);
token.transfer(bob, 1000);
```

## Differential Testing

### Compare Against Reference Implementation
```solidity
contract DifferentialTest is Test {
    OriginalContract original;
    OptimizedContract optimized;
    
    function setUp() public {
        original = new OriginalContract();
        optimized = new OptimizedContract();
    }
    
    function testFuzzDifferential(uint256 input) public {
        vm.assume(input > 0 && input < type(uint128).max);
        
        uint256 originalResult = original.calculate(input);
        uint256 optimizedResult = optimized.calculate(input);
        
        assertEq(originalResult, optimizedResult, "Results diverged");
    }
}
```

## Fork Testing for DeFi Protocols

### Test Against Live Protocols
```solidity
contract ForkTest is Test {
    IERC20 constant USDC = IERC20(0xA0b86a33E6441b4f71eeD0bf3FD624A731F2F7b7);
    IUniswapV2Router constant ROUTER = IUniswapV2Router(0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D);
    
    address constant WHALE = 0x47ac0Fb4F2D84898e4D9E7b4DaB3C24507a6D503;
    
    function setUp() public {
        vm.createFork(vm.envString("ETH_RPC_URL"));
        vm.rollFork(18500000); // Specific block for consistent testing
    }
    
    function testFlashLoanAttack() public {
        uint256 initialBalance = USDC.balanceOf(WHALE);
        
        // Simulate flash loan attack
        vm.startPrank(WHALE);
        
        // Attack implementation
        uint256 borrowAmount = 1000000e6; // 1M USDC
        // ... attack logic ...
        
        vm.stopPrank();
        
        uint256 finalBalance = USDC.balanceOf(WHALE);
        assertLt(finalBalance, initialBalance, "Attack should not be profitable");
    }
}
```

## Gas Analysis and Optimization

### Detailed Gas Reporting
```bash
# Generate detailed gas report
forge test --gas-report --json > gas-report.json

# Compare gas usage between implementations
forge snapshot
# Make changes
forge snapshot --diff
```

### Gas Optimization Testing
```solidity
contract GasOptimizationTest is Test {
    function testGasOptimization() public {
        uint256 gasBefore = gasleft();
        
        // Original implementation
        originalFunction();
        
        uint256 gasUsedOriginal = gasBefore - gasleft();
        
        gasBefore = gasleft();
        
        // Optimized implementation
        optimizedFunction();
        
        uint256 gasUsedOptimized = gasBefore - gasleft();
        
        assertLt(gasUsedOptimized, gasUsedOriginal, "Optimization should use less gas");
    }
}
```

## Debugging with Forge

### Debug Failed Tests
```bash
# Debug specific test with traces
forge test --match-test testFailingFunction -vvvv

# Debug with structured logs
forge test --match-test testFailingFunction --debug
```

### Step-by-Step Debugging
```solidity
contract DebugTest is Test {
    function testDebugExample() public {
        console.log("Starting test");
        
        uint256 value = 100;
        console.log("Initial value:", value);
        
        value = complexCalculation(value);
        console.log("After calculation:", value);
        
        assertEq(value, 150);
    }
}
```

## CI/CD Integration

### GitHub Actions Example
```yaml
name: Forge Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
      with:
        submodules: recursive
    
    - name: Install Foundry
      uses: foundry-rs/foundry-toolchain@v1
    
    - name: Install dependencies
      run: forge install
    
    - name: Run tests
      run: forge test -vvv
      env:
        ETH_RPC_URL: ${{ secrets.ETH_RPC_URL }}
    
    - name: Generate gas report
      run: forge test --gas-report > gas-report.txt
    
    - name: Upload gas report
      uses: actions/upload-artifact@v3
      with:
        name: gas-report
        path: gas-report.txt
```

## Best Practices for Audit Testing

### 1. Comprehensive Test Coverage
```bash
# Generate coverage report
forge coverage
forge coverage --report lcov
```

### 2. Property-Based Testing
- Test with random inputs to find edge cases
- Use `vm.assume()` to set valid input constraints
- Focus on invariants that should always hold

### 3. Integration Testing
- Test contract interactions with real protocols
- Use fork testing for DeFi integrations
- Simulate various market conditions

### 4. Edge Case Testing
```solidity
function testEdgeCases() public {
    // Test maximum values
    testFunction(type(uint256).max);
    
    // Test minimum values
    testFunction(0);
    
    // Test boundary conditions
    testFunction(CONTRACT_LIMIT - 1);
    testFunction(CONTRACT_LIMIT);
    testFunction(CONTRACT_LIMIT + 1);
}
```

### 5. Failure Mode Testing
```solidity
function testFailureModes() public {
    // Test when external calls fail
    vm.mockCall(externalContract, abi.encodeWithSelector(ExternalContract.getData.selector), abi.encode(false));
    
    // Test when out of gas
    vm.expectRevert();
    expensiveFunction{gas: 1000}();
}
```

## Common Forge Commands for Auditing

| Command | Purpose |
|---------|---------|
| `forge test` | Run all tests |
| `forge test -vvv` | Verbose test output |
| `forge test --gas-report` | Generate gas usage report |
| `forge test --fork-url URL` | Test against forked network |
| `forge coverage` | Generate test coverage report |
| `forge snapshot` | Create gas snapshot |
| `forge debug` | Debug failed tests |
| `forge bind` | Generate Rust bindings |
| `forge verify-contract` | Verify contracts on Etherscan |

Remember: Forge is most powerful when combined with comprehensive test scenarios that cover normal operation, edge cases, attack vectors, and failure modes. The goal is to prove the contract behaves correctly under all possible conditions. 