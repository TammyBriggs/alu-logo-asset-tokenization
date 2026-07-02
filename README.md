# ALU Logo Asset Tokenisation Project

## Project Overview
This project implements a blockchain-based digital asset protection and ownership distribution system built for the African Leadership University (ALU). It utilizes two smart contracts that work sequentially/in tandem:

1. **ALUAssetRegistry.sol (ERC-721):** Acts as a secure, immutable public ledger that anchors the physical ALU logo file to the Ethereum blockchain by storing its unique cryptographic fingerprint. This prevents counterfeits and unauthorized alterations, as any modification to the file would change its hash.
2. **ALULogoToken.sol (ERC-20):** Acts as the equity layer, taking the singular ownership rights established by the registry and fractionalizing them into 1,000,000 fungible "ALUT" shares. This allows the university to distribute ownership stakes among multiple stakeholders.

Together, these contracts prove the authenticity of the digital asset while allowing its ownership to be securely divided and distributed.

## Verifiable Asset Hash
The official ALU logo has been cryptographically hashed to establish its digital fingerprint. Anyone can independently verify the asset by passing the original file through a SHA-256 generator and comparing the result to this registered on-chain signature. If the file is modified in any way, this hash will permanently change, making counterfeits instantly detectable.

* **Registered SHA-256 Hash:** `0xac588b92aaed6542a2c537fe0e4ad264095811768e017f74228535d8ad9ecc9b`

## Tech Stack & Versions
* **Solidity:** `^0.8.24`
* **Hardhat:** `v2` (specifically using `@nomicfoundation/hardhat-toolbox`)
* **OpenZeppelin Contracts:** `^5.0.0`
* **Ethers.js:** `v6`
* **Testing:** Chai / Mocha

## Setup and Usage Instructions

### 1. Install Dependencies
Ensure you have Node.js installed. Clone the repository and install the required packages (including Hardhat and OpenZeppelin):
```bash
npm install
```

### 2. Compile the Smart Contracts
Compile the Solidity files into machine-readable artifacts and ABI files:
```bash
npx hardhat clean
npx hardhat compile
```

### 3. Run the Automated Test Suite
Execute the 8 automated tests to verify the logic of both the ERC-721 and ERC-20 contracts (including registry checks, duplicate rejections, and fractional ownership math):
```bash
npx hardhat test
```

### 4. Deploy to Local Blockchain
First, start up your local Hardhat test network in a terminal window:
```bash
npx hardhat node
```
Leave that terminal running. Open a second terminal window and execute the deployment script to deploy the contracts, register the logo, and mint the tokens:
```bash
npx hardhat run scripts/deploy.js --network localhost
```

## Development Challenges & Resolutions
During the development of this project, I encountered and resolved several technical issues:

**1. Hardhat 3 vs Hardhat 2 Initialization Issue**
- *Problem:* During initial project setup, the standard JavaScript project initialization options were missing from the CLI.
- *Resolution:* This occurred because my environment was defaulting to Hardhat 3 instead of Hardhat 2. I resolved the issue by explicitly configuring the project to use Hardhat 2, which restored the necessary JavaScript scaffolding options.

**2. EVM Version Incompatibility (`mcopy` instruction error)**
- *Problem:* When initially compiling, Hardhat threw an error regarding the `mcopy` instruction. OpenZeppelin's newest libraries utilize this opcode from the `cancun` Ethereum Virtual Machine upgrade, but my Hardhat config defaulted to the older `paris` EVM.
- *Resolution:* I updated `hardhat.config.js` to explicitly set `evmVersion: "cancun"` and upgraded the Solidity compiler target to `0.8.24` to satisfy the OpenZeppelin dependency requirements.

**3. Ethers.js v5 vs v6 Syntax Breaking Changes**
- *Problem:* My automated test suite failed with `TypeError: Cannot read properties of undefined (reading 'solidityKeccak256')`. This happened because Hardhat installed Ethers.js v6, which removed the `.utils` object used in older v5 syntax.
- *Resolution:* I migrated the test suite syntax to Ethers v6 standards — replacing `ethers.utils.solidityKeccak256` with `ethers.solidityPackedKeccak256`, updating `.deployed()` to `.waitForDeployment()`, and ensuring token balances were asserted using BigInt formats.

**4. Artifact Not Found Error**
- *Problem:* While running the final tests, Hardhat threw an `Artifact for contract "ALULogoToken" not found` error.
- *Resolution:* This occurred because the test suite was attempting to test the Part 2 contract before the `.sol` file had been properly created and compiled into the `contracts/` directory. I created the file, ran `npx hardhat clean` to flush the old cache, and recompiled the environment, which successfully generated the missing artifact.

**5. Nested Git Directory Conflicts**
- *Problem:* My Hardhat environment was originally initialized in a sub-folder inside my main Git repository, preventing proper version control tracking and deployment.
- *Resolution:* I used terminal commands to flatten the directory structure, moving all configuration files to the root level of the repository, allowing GitHub to track the project correctly.
```
