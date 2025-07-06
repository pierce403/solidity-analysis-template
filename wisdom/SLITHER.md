# Slither - Static Analysis Tool for Solidity

Slither is a static analysis framework for Solidity that runs a suite of vulnerability detectors and provides detailed information about contract issues.

## Installation

### Method 1: Using pip (Recommended)
```bash
pip3 install slither-analyzer
```

### Method 2: Using pipx (Isolated environment)
```bash
pipx install slither-analyzer
```

### Method 3: From source
```bash
git clone https://github.com/crytic/slither.git
cd slither
python3 setup.py install
```

### Dependencies
Slither requires:
- Python 3.8+
- Solidity compiler (solc)

Install solc:
```bash
# Using solc-select (recommended for managing versions)
pip3 install solc-select
solc-select install 0.8.19
solc-select use 0.8.19
```

## Basic Usage

### Analyze a single contract
```bash
slither contracts/MyContract.sol
```

### Analyze all contracts in a directory
```bash
slither .
```

### Analyze with specific Solidity version
```bash
slither . --solc-version 0.8.19
```

## Essential Audit Commands

### 1. Full Analysis with Output to File
```bash
slither . --json slither-report.json --sarif slither-report.sarif
```

### 2. Filter by Severity
```bash
# Only high and medium severity issues
slither . --filter-paths "node_modules|test" --exclude-informational --exclude-low
```

### 3. Focus on Specific Detectors
```bash
# Only reentrancy and access control
slither . --include-detectors reentrancy-eth,suicidal,arbitrary-send-eth
```

### 4. Exclude False Positives
```bash
# Exclude test files and libraries
slither . --filter-paths "test/|Test\.sol|Mock\.sol|node_modules/"
```

### 5. Generate Human-Readable Report
```bash
slither . --print human-summary > slither-summary.txt
```

## Advanced Auditing Techniques

### Custom Configuration File
Create `slither.config.json`:
```json
{
  "filter_paths": ["test/", "node_modules/", "lib/"],
  "exclude_informational": true,
  "exclude_low": false,
  "detectors_to_exclude": ["naming-convention", "pragma"],
  "detectors_to_include": ["reentrancy-eth", "arbitrary-send-eth"]
}
```

Run with config:
```bash
slither . --config-file slither.config.json
```

### Inheritance and Function Analysis
```bash
# Print inheritance graph
slither . --print inheritance-graph

# Print function summaries
slither . --print function-summary

# Print contract summaries
slither . --print contract-summary
```

### Gas and Optimization Analysis
```bash
# Identify costly operations
slither . --print costly-operations

# Find unused return values
slither . --include-detectors unused-return

# Detect dead code
slither . --include-detectors dead-code
```

## Key Detectors for Auditing

### Critical Severity
- `reentrancy-eth`: Reentrancy vulnerabilities with ETH transfer
- `reentrancy-no-eth`: Reentrancy vulnerabilities without ETH transfer
- `suicidal`: Functions allowing anyone to destroy the contract
- `arbitrary-send-eth`: Functions that send Ether to arbitrary destinations

### High Severity
- `incorrect-equality`: Dangerous strict equalities
- `locked-ether`: Contracts that lock Ether without withdrawal mechanism
- `delegatecall-loop`: Delegatecall inside a loop
- `msg-value-loop`: msg.value inside a loop

### Medium Severity
- `unchecked-transfer`: Unchecked return value of transfer calls
- `weak-prng`: Weak PRNG due to a modulo on block.timestamp
- `calls-loop`: Multiple calls in a loop
- `timestamp`: Dangerous usage of block.timestamp

## Practical Audit Workflow

### 1. Initial Quick Scan
```bash
# Get high-level overview
slither . --exclude-informational --exclude-low --filter-paths "test/|node_modules/"
```

### 2. Detailed Analysis
```bash
# Full analysis with all detectors
slither . --json detailed-report.json --print contract-summary,function-summary
```

### 3. Focus on High-Risk Areas
```bash
# Target specific vulnerability classes
slither . --include-detectors reentrancy-eth,reentrancy-no-eth,arbitrary-send-eth,suicidal
```

### 4. Gas and Optimization Review
```bash
slither . --include-detectors costly-operations,dead-code,unused-return
```

## Integration with CI/CD

### GitHub Actions Example
```yaml
name: Slither Analysis
on: [push, pull_request]
jobs:
  analyze:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Run Slither
      uses: crytic/slither-action@v0.3.0
      with:
        node-version: 16
        sarif: results.sarif
        fail-on: high
```

## Common Issues and Solutions

### Issue: "Source not found" errors
**Solution:** Ensure proper imports and compilation setup
```bash
# For Hardhat projects
slither . --hardhat-cache-clear
slither . --foundry-compile-all

# For Foundry projects  
forge build
slither . --foundry-out-dir out
```

### Issue: Too many false positives
**Solution:** Use filtering and custom configurations
```bash
slither . --filter-paths "test/|Test\.sol|Mock\.sol" --exclude-informational
```

### Issue: Missing some detectors
**Solution:** Update Slither regularly and check available detectors
```bash
pip3 install --upgrade slither-analyzer
slither --list-detectors
```

## Best Practices for Auditing

1. **Start with clean compilation**: Ensure project compiles without warnings
2. **Use version-specific analysis**: Pin Solidity version for consistent results
3. **Filter noise**: Exclude test files and known safe libraries
4. **Focus on severity**: Start with critical/high, then work down
5. **Validate findings**: Not all Slither findings are exploitable - verify manually
6. **Regular updates**: Keep Slither updated for latest detectors
7. **Combine with other tools**: Use alongside Mythril, manual review, and testing

## Sample Output Analysis

When Slither finds issues, analyze them systematically:

```
Contract.sol:45-52: reentrancy-eth (high)
    Reentrancy in Contract.withdraw():
        External calls:
        - msg.sender.call{value: amount}() (Contract.sol:47)
        State variables written after the call(s):
        - balances[msg.sender] = 0 (Contract.sol:48)
```

**Action Items:**
1. Verify the reentrancy is exploitable
2. Check if CEI pattern is followed
3. Write proof-of-concept if vulnerable
4. Recommend fix (ReentrancyGuard, CEI pattern)

## Useful Slither Flags Reference

| Flag | Purpose |
|------|---------|
| `--json output.json` | JSON output for tooling |
| `--sarif output.sarif` | SARIF format for CI/CD |
| `--exclude-low` | Skip low severity findings |
| `--exclude-informational` | Skip informational findings |
| `--filter-paths "pattern"` | Exclude files matching pattern |
| `--include-detectors "list"` | Only run specific detectors |
| `--exclude-detectors "list"` | Skip specific detectors |
| `--print "printer"` | Use specific printer output |
| `--list-detectors` | Show all available detectors |

Remember: Slither is a powerful starting point for audits, but manual verification of all findings is essential for accurate security assessment. 