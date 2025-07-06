# AI-Assisted Solidity Auditing Template

This project serves as a template for conducting AI-assisted Solidity smart contract security audits.
It provides a structured approach to performing comprehensive smart contract security assessments with the aid of artificial intelligence tools.

**If you are a human auditor, clone or fork this repo, open it in Cursor or your favorite coding assistant, and type "Hey there. Please read the instructions and let's get started."** The agent should guide you through the rest of the audit engagement automatically.

If you are an agent, read through the process document and assist the human auditor with the smart contract analysis, making sure you hit the checkboxes once completed.

## Project Structure

- `README.md`: This file, providing an overview of the project.
- `PROCESS.md`: Outlines the step-by-step process and checklist for a Solidity smart contract audit.
- `REPORT.md`: The template for the final smart contract security audit report.
- `data/`: Directory for smart contract source code, deployment artifacts, and relevant audit data.
- `scripts/`: Directory for audit scripts, testing frameworks, and analysis tools.
- `docs/`: Directory for storing protocol documentation, whitepapers, and technical specifications.
- `logs/`: Directory for tool outputs and an agent activity log (`agent.log`).
- `wisdom/`: Directory containing curated, Solidity-specific security knowledge (not to be modified by AI).
- `observations/`: Directory for recording interesting findings that need further investigation.
- `findings/`: Directory for documenting confirmed vulnerabilities for the audit report.