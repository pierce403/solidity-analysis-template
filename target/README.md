# Target Directory

This directory is where the **target smart contract code** for audit should be placed and analyzed. This is the core working directory for all audit activities.

## Purpose

The `target/` directory serves as the isolated workspace for:
- Target smart contract source code
- Build artifacts and compilation outputs  
- Test execution and custom security tests
- Static analysis tool outputs
- All audit-related analysis of the target codebase

## Setup Process

### 1. Repository Acquisition
```bash
# Clone the target repository into this directory
cd target/
git clone <TARGET_REPO_URL> .

# Or if target/ already exists:
git clone <TARGET_REPO_URL> protocol-name/
cd protocol-name/
```

### 2. Checkout Specific Commit
```bash
# Always work from a specific commit hash for audit consistency
git checkout <COMMIT_HASH>

# Verify you're on the correct commit
git rev-parse HEAD
```

### 3. Environment Setup
```bash
# Install dependencies
npm install
# or
yarn install

# For Foundry projects
forge install

# For Hardhat projects  
npx hardhat compile
```

## Directory Structure After Setup

```
target/
├── src/                    # Smart contract source code
├── contracts/              # Alternative contracts directory
├── test/                   # Existing test suites
├── scripts/               # Deployment and utility scripts
├── lib/                   # Dependencies (Foundry)
├── node_modules/          # Dependencies (npm/yarn)
├── foundry.toml           # Foundry configuration
├── hardhat.config.js      # Hardhat configuration
├── package.json           # Project dependencies
└── README.md              # Target project documentation
```

## Audit Workflow in Target Directory

### Phase 1: Verification
1. **Build Verification**
   ```bash
   # Ensure project compiles cleanly
   forge build
   # or
   npx hardhat compile
   ```

2. **Test Suite Validation**
   ```bash
   # Run existing tests to ensure they pass
   forge test
   # or  
   npx hardhat test
   ```

3. **Coverage Analysis**
   ```bash
   # Generate test coverage report
   forge coverage
   # or
   npx hardhat coverage
   ```

### Phase 2: Static Analysis
1. **Slither Analysis**
   ```bash
   # Run comprehensive Slither analysis
   slither . --json ../logs/slither-report.json
   slither . --exclude-informational --exclude-low
   ```

2. **Mythril Analysis**
   ```bash
   # Run Mythril on critical contracts
   myth analyze src/MainContract.sol --execution-timeout 300
   ```

### Phase 3: Security Testing
1. **Custom Security Tests**
   ```bash
   # Create additional test files for security scenarios
   # Focus on money flow, reentrancy, access control
   forge test test/audit/SecurityTests.t.sol -vvv
   ```

2. **Fork Testing** (for DeFi protocols)
   ```bash
   # Test against mainnet forks
   forge test --fork-url $ETH_RPC_URL --fork-block-number 18500000
   ```

## Important Guidelines

### ⚠️ **Security Considerations**
- **Never modify target code** except for creating proof-of-concept tests
- **Always work from a specific commit hash** for audit consistency
- **Isolate target code** - don't mix with your own analysis scripts
- **Document any build issues** that could affect security

### 📝 **Documentation Requirements**
- Record the exact commit hash being audited
- Document any dependencies or build modifications needed
- Note any failing tests or compilation warnings
- Track all static analysis tool outputs

### 🔍 **Analysis Focus Areas**
When working in the target directory, pay special attention to:

1. **Money Flow Functions**
   - Deposit/withdrawal mechanisms
   - Token minting/burning
   - Fee collection and distribution
   - Emergency fund recovery

2. **State Modification Functions**
   - Balance updates
   - Permission changes  
   - Protocol parameter modifications
   - Upgrade mechanisms

3. **External Integrations**
   - Oracle usage
   - Cross-contract calls
   - External protocol interactions
   - Bridge mechanisms

## File Organization

### Keep Target Code Clean
```bash
target/
├── protocol-contracts/     # Original target code (DO NOT MODIFY)
├── audit-tests/           # Your custom security tests
├── analysis-outputs/      # Tool outputs and reports
└── proof-of-concepts/     # PoC exploit code
```

### Recommended Subdirectories
- `audit-tests/` - Custom security test files
- `analysis-outputs/` - Static analysis results
- `proof-of-concepts/` - Exploit demonstrations
- `gas-analysis/` - Gas usage reports and optimizations

## Integration with Main Audit Process

### Log All Activities
Document every action in the main audit log:
```bash
# Example log entries
echo "[$(date)] - Cloned target repo to target/ at commit abc123" >> ../logs/agent.log
echo "[$(date)] - Build successful with Foundry, 15 contracts compiled" >> ../logs/agent.log
echo "[$(date)] - Slither analysis completed: 12 findings identified" >> ../logs/agent.log
```

### Save Outputs to Main Logs
```bash
# Copy important outputs to main logs directory
cp slither-report.json ../logs/
cp mythril-output.txt ../logs/
cp test-coverage.html ../logs/
```

## Common Commands Reference

```bash
# Build and test
forge build && forge test

# Static analysis
slither . --filter-paths "test/|lib/" --exclude-informational

# Gas analysis  
forge test --gas-report

# Coverage
forge coverage --report lcov

# Specific contract analysis
myth analyze src/VulnerableContract.sol --execution-timeout 600

# Fork testing
forge test --fork-url $ETH_RPC_URL test/ForkTests.t.sol
```

## Remember

The `target/` directory is your **primary analysis workspace**. All security analysis should be conducted here while maintaining the integrity of the original codebase. Think of this as a sterile environment where you can safely analyze, test, and probe the target contracts without affecting the main audit template structure.

**Goal**: Identify vulnerabilities that criminals could exploit to steal funds or compromise the protocol. 