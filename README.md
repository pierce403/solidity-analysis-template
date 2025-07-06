# AI-Assisted Solidity Auditing Template

This project serves as a template for conducting AI-assisted Solidity smart contract security audits.
It provides a structured approach to performing comprehensive smart contract security assessments with the aid of artificial intelligence tools.

**If you are a human auditor, clone or fork this repo, open it in Cursor or your favorite coding assistant, and type "Hey there. Please read the instructions and let's get started."** The agent should guide you through the rest of the audit engagement automatically.

If you are an agent, read through the process document and assist the human auditor with the smart contract analysis, making sure you hit the checkboxes once completed.

## ⚠️ Important: Audit Data Protection

This template includes a comprehensive `.gitignore` file that **automatically protects sensitive audit data** from being committed to version control. The following directories are excluded:

- `target/` - Target smart contract code being audited
- `data/` - Audit artifacts and analysis data  
- `observations/` - Sensitive vulnerability observations
- `findings/` - Confirmed security vulnerabilities
- `logs/` - Tool outputs and agent activity logs
- `scripts/` - Custom audit scripts and tools

**This protection ensures that:**
✅ Confidential audit findings remain private  
✅ Target project code isn't accidentally exposed  
✅ Proprietary analysis tools stay secure  
✅ Client data remains protected  

When forking this template for audits, these directories will remain local to your machine and won't be committed to your audit repository.

## Project Structure

- `README.md`: This file, providing an overview of the project.
- `PROCESS.md`: Outlines the step-by-step process and checklist for a Solidity smart contract audit.
- `REPORT.md`: The template for the final smart contract security audit report.
- `target/`: **Primary workspace** - Clone target repositories here for analysis (gitignored).
- `data/`: Directory for smart contract source code, deployment artifacts, and relevant audit data (gitignored).
- `scripts/`: Directory for audit scripts, testing frameworks, and analysis tools (gitignored).
- `docs/`: Directory for storing protocol documentation, whitepapers, and technical specifications.
- `logs/`: Directory for tool outputs and an agent activity log (`agent.log`) (gitignored).
- `wisdom/`: Directory containing curated, Solidity-specific security knowledge (not to be modified by AI).
- `observations/`: Directory for recording interesting findings that need further investigation (gitignored).
- `findings/`: Directory for documenting confirmed vulnerabilities for the audit report (gitignored).