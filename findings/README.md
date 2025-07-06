# Findings Directory

This directory is for documenting confirmed smart contract vulnerabilities or significant security issues that will be included in the final audit `REPORT.md`.

Each finding should be well-documented and typically include:
- **Title:** A clear and concise name for the vulnerability (e.g., "Reentrancy in withdraw function").
- **Severity:** (Critical, High, Medium, Low, Informational) - based on funds at risk and exploitability.
- **Contract:** The specific smart contract(s) affected.
- **Function:** The specific function(s) or code section where the vulnerability exists.
- **Description:** A detailed technical explanation of the vulnerability and its root cause.
- **Impact:** The potential impact if exploited (e.g., "All user funds can be drained").
- **Proof of Concept:** Solidity code or test case demonstrating the vulnerability.
- **Affected Lines:** Specific line numbers in the source code.
- **Recommendations:** Specific code changes or mitigation strategies to fix the vulnerability.
- **Status:** Current status (Open/Acknowledged/Fixed/Will Not Fix).

## Smart Contract Specific Considerations:
- Include gas implications and potential DoS vectors
- Consider economic attack vectors and MEV exploitation
- Document any upgrade mechanism implications
- Note centralization risks or admin privilege issues
- Include on-chain transaction examples if applicable

These documents form the core of the smart contract audit's outcomes and should be written with technical precision suitable for developers and protocol teams. 