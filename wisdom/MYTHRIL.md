# Mythril - Symbolic Execution Tool for Ethereum Smart Contracts

Mythril is a security analysis tool for Ethereum smart contracts that uses symbolic execution, taint analysis, and control flow checking to detect security vulnerabilities.

## Installation

### Method 1: Using pip (Recommended)
```bash
pip3 install mythril
```

### Method 2: Using Docker
```bash
docker pull mythril/myth
```

### Method 3: From source
```bash
git clone https://github.com/ConsenSys/mythril.git
cd mythril
pip3 install -r requirements.txt
python3 setup.py install
```

### Dependencies
Mythril requires:
- Python 3.7+
- Solidity compiler (solc)
- Z3 theorem prover (installed automatically with pip)

## Basic Usage

### Analyze Solidity source file
```bash
myth analyze MyContract.sol
```

### Analyze compiled bytecode
```bash
myth analyze -c "0x606060405234610000575b60405161..."
```

### Analyze deployed contract
```bash
myth analyze -a 0x1234567890123456789012345678901234567890
```

## Essential Audit Commands

### 1. Deep Analysis with Custom Gas Limit
```bash
myth analyze MyContract.sol --execution-timeout 300 --max-depth 100
```

### 2. Focus on Specific Modules
```bash
# Only check for reentrancy and integer overflow
myth analyze MyContract.sol -m ether_thief,integer
```

### 3. Generate Detailed Report
```bash
myth analyze MyContract.sol -o json > mythril-report.json
myth analyze MyContract.sol -o markdown > mythril-report.md
```

### 4. Transaction Sequence Analysis
```bash
myth analyze MyContract.sol --transaction-count 3
```

### 5. Custom Solver Timeout
```bash
myth analyze MyContract.sol --solver-timeout 30000
```

## Advanced Auditing Techniques

### Symbolic Execution Configuration
```bash
# Increase depth for thorough analysis
myth analyze MyContract.sol --max-depth 50 --execution-timeout 600

# Focus on specific functions
myth analyze MyContract.sol --strategy bfs --max-depth 20
```

### Multi-Contract Analysis
```bash
# Analyze with dependencies
myth analyze . --solv 0.8.19 --execution-timeout 600
```

### Memory and Performance Optimization
```bash
# For large contracts
myth analyze MyContract.sol --pruning-factor 1 --solver-timeout 10000
```

## Key Vulnerability Modules

### Critical Vulnerabilities
- **`ether_thief`**: Unauthorized Ether withdrawal
- **`suicide`**: Unrestricted self-destruct
- **`arbitrary_jump`**: Arbitrary jump vulnerabilities

### High Severity
- **`integer`**: Integer overflow/underflow
- **`multiple_sends`**: Multiple Ether sends in single transaction
- **`external_calls`**: Dangerous external calls

### Medium Severity
- **`weak_randomness`**: Weak sources of randomness
- **`dependence_on_predictable_vars`**: Dependence on block variables
- **`deprecated_opcodes`**: Usage of deprecated opcodes

## Practical Audit Workflow

### 1. Initial Scan
```bash
# Quick overview with standard modules
myth analyze Contract.sol --execution-timeout 120 -m ether_thief,integer,suicide
```

### 2. Deep Dive Analysis
```bash
# Comprehensive analysis
myth analyze Contract.sol \
  --execution-timeout 600 \
  --max-depth 100 \
  --transaction-count 5 \
  --solver-timeout 30000 \
  -o json > deep-analysis.json
```

### 3. Function-Specific Analysis
```bash
# Target specific functions (manual bytecode analysis)
myth analyze -c "bytecode" --strategy dfs --max-depth 30
```

### 4. Gas Analysis Integration
```bash
# Combine with gas analysis
myth analyze Contract.sol --enable-physics --solver-timeout 20000
```

## Integration with Development Workflow

### Hardhat Integration
```javascript
// hardhat.config.js
module.exports = {
  solidity: "0.8.19",
  mythril: {
    debug: false,
    full: true,
    timeout: 300
  }
};
```

### Foundry Integration
```bash
# After forge build
myth analyze out/Contract.sol/Contract.json
```

### CI/CD Pipeline
```yaml
name: Mythril Analysis
on: [push, pull_request]
jobs:
  mythril:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Setup Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'
    - name: Install Mythril
      run: pip install mythril
    - name: Run Mythril
      run: |
        myth analyze contracts/ --execution-timeout 300 -o json > mythril-results.json
    - name: Upload results
      uses: actions/upload-artifact@v3
      with:
        name: mythril-results
        path: mythril-results.json
```

## Advanced Configuration

### Custom Analysis Strategy
```bash
# Breadth-first search for wide coverage
myth analyze Contract.sol --strategy bfs --max-depth 50

# Depth-first search for deep paths
myth analyze Contract.sol --strategy dfs --max-depth 100
```

### Memory Management
```bash
# For contracts with complex state
myth analyze Contract.sol \
  --pruning-factor 0.8 \
  --solver-timeout 25000 \
  --execution-timeout 900
```

### Custom Module Selection
```bash
# Create custom module set for specific audit focus
myth analyze Contract.sol \
  -m ether_thief,integer,suicide,multiple_sends,external_calls \
  --execution-timeout 300
```

## Docker Usage

### Basic Docker Analysis
```bash
docker run -v $(pwd):/tmp mythril/myth analyze /tmp/Contract.sol
```

### Docker with Custom Configuration
```bash
docker run -v $(pwd):/tmp mythril/myth analyze \
  /tmp/Contract.sol \
  --execution-timeout 300 \
  --max-depth 50 \
  -o json
```

## Common Issues and Solutions

### Issue: Analysis timeout
**Solution:** Reduce complexity or increase timeout
```bash
myth analyze Contract.sol --execution-timeout 900 --solver-timeout 45000
```

### Issue: Memory exhaustion
**Solution:** Use pruning and limit depth
```bash
myth analyze Contract.sol --pruning-factor 0.5 --max-depth 30
```

### Issue: False positives
**Solution:** Analyze specific modules and verify manually
```bash
myth analyze Contract.sol -m ether_thief,integer --transaction-count 2
```

### Issue: Solidity version conflicts
**Solution:** Specify Solidity version
```bash
myth analyze Contract.sol --solv 0.8.19
```

## Interpreting Results

### Understanding Mythril Output

```
==== Integer Arithmetic Bugs ====
SWC ID: 101
Severity: High
Contract: MyContract
Function name: withdraw(uint256)
PC address: 722
Estimated Gas Usage: 1234 - 5678
Description: The arithmetic operation can result in integer overflow.
```

**Analysis Steps:**
1. **Verify the vulnerability**: Check if overflow is actually possible
2. **Assess impact**: Determine what happens if overflow occurs
3. **Create PoC**: Write test case demonstrating the issue
4. **Recommend fix**: Suggest SafeMath or Solidity 0.8+ overflow checks

## Best Practices for Mythril Auditing

1. **Start with focused modules**: Don't run all modules initially
2. **Incremental analysis**: Start with shorter timeouts, increase as needed
3. **Combine with static analysis**: Use with Slither for comprehensive coverage
4. **Manual verification required**: All Mythril findings need manual verification
5. **Performance tuning**: Adjust depth and timeout based on contract complexity
6. **Regular updates**: Keep Mythril updated for latest vulnerability patterns

## Performance Optimization Tips

### For Large Contracts
```bash
# Optimize for large codebase
myth analyze LargeContract.sol \
  --pruning-factor 0.3 \
  --max-depth 25 \
  --execution-timeout 1200 \
  --strategy bfs
```

### For Complex State
```bash
# Handle complex state interactions
myth analyze ComplexContract.sol \
  --transaction-count 4 \
  --solver-timeout 40000 \
  --enable-physics
```

### Parallel Analysis
```bash
# Analyze multiple contracts in parallel
for contract in contracts/*.sol; do
    myth analyze "$contract" --execution-timeout 300 -o json > "${contract%.sol}-mythril.json" &
done
wait
```

## Useful Mythril Flags Reference

| Flag | Purpose |
|------|---------|
| `--execution-timeout N` | Maximum execution time in seconds |
| `--max-depth N` | Maximum recursion depth |
| `--transaction-count N` | Number of transactions to analyze |
| `--solver-timeout N` | Z3 solver timeout in milliseconds |
| `-m modules` | Specific vulnerability modules to run |
| `--strategy bfs/dfs` | Search strategy (breadth/depth first) |
| `--pruning-factor N` | State pruning factor (0.0-1.0) |
| `-o format` | Output format (text/json/markdown) |
| `--enable-physics` | Enable gas physics simulation |
| `--solv version` | Solidity compiler version |

## Mythril vs Other Tools

**Mythril Strengths:**
- Deep symbolic execution analysis
- Finds complex logical vulnerabilities
- Good for state-dependent issues
- Excellent for arithmetic bugs

**Mythril Limitations:**
- Resource intensive
- Can produce false positives
- Limited by execution timeout
- May miss some static patterns

**Best Used With:**
- Slither (static analysis)
- Manual code review
- Unit testing with edge cases
- Formal verification for critical functions

Remember: Mythril's symbolic execution is powerful but computationally expensive. Use it strategically on critical contract functions and always verify findings manually. 