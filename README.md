# Smart Contract Security Portfolio
### Harvesto Orlando — Smart Contract Auditor

---

## About

I'm a smart contract auditor specialising in Solidity, focused on finding high and critical severity vulnerabilities across DeFi protocols, bridges, and on-chain infrastructure. I've been conducting security research since November 2025, covering protocols across 12+ chains including Ethereum, Base, Arbitrum, BSC, Kaia, HyperEVM, Polkadot/Substrate, and more.

My approach combines deep manual code review with active PoC development using Foundry fork tests to validate exploitability before disclosure. I also bring a background in blockchain technical writing and marketing, having produced technical documentation for web3 protocols — a combination that lets me communicate complex vulnerabilities clearly to both technical and non-technical stakeholders.

**Twitter/X:** [@Harvesto12](https://x.com/Harvesto12)
**Contact:** blockchaincopywriter@gmail.com

---

## Services

I work with a small number of protocols at a time. Reach out before engaging.

Available for private security audits, collaboration, and security-focused technical documentation.

| Service | Description |
|---------|-------------|
| **Smart Contract Audit (Solidity)** | Manual security review with PoC validation — full report delivered covering all severity findings |
| **Ultimate Audit Preparation** | Full pre-audit readiness review — codebase organisation, natspec, test coverage gaps, and documentation quality so your audit spend goes further |
| **Xray** | Protocol architecture mapping, threat modelling, and invariant definition before a single line is audited |
| **Security Consultation** | Targeted advisory on specific components, attack surfaces, or architectural decisions |
| **Technical Documentation & Audit Writing** | Security-focused docs, audit reports, and written disclosure packages |
| **Audit Post-Mortems** | Root cause analysis, exploit breakdowns, and remediation write-ups for past incidents or findings |

---

## Finding Summary

| Severity | Confirmed (Bug Bounty) | Independent Research |
|----------|----------------------|----------------------|
| Critical | — | 2 |
| High | 3 (HackenProof) | 10+ |
| Medium | — | 30+ |

> **On independent research:** All findings were identified through independent security research. Findings were responsibly disclosed to the relevant protocol teams where necessary. Validity was assessed through root cause analysis, on-chain verification, and Foundry PoC tests where applicable.

---

## Confirmed Bug Bounty Findings

### HackenProof — 3x High Severity
Submitted and confirmed across an active bug bounty program.

---

## Glider Query Database Contributions

[Glider](https://glide.r.xyz) is a code search engine for EVM smart contracts — securing every chain in Web3 by catching real Solidity bugs at scale. Accepted queries earn recognition and cash prizes.

[View my leaderboard profile →](https://r.xyz/glider-query-database/leaderboard/efaf48cf-79ca-4425-a677-209dc1e94fa0)

| Query | Rarity |
|-------|--------|
| Exploitable setFee Function | Rare |
| Single-source oracle compromise — attacker directly changes feed value | Rare |
| Detect approve() calls where spender is arbitrary | Uncommon |
| Delegatecall target (address state variable) updatable by any user without authentication | Uncommon |
| Initialize functions callable more than once — missing initializer modifier or initialized guard | Uncommon |
| Unprotected ownership and admin transfer functions lacking access control | Uncommon |

---

## Independent Research Findings

### 🔴 Critical

**Treasury Drain via Default State Initialization**
- **Class:** State Initialization / Default Value
- **Chain:** Polkadot (Substrate/Rust)
- **Impact:** A default zero value on first consensus update allowed a relayer to claim rewards for all blocks since genesis in a single transaction, draining the protocol treasury entirely. On-chain precondition confirmed satisfied.

**Novel Harvest-the-Pair Attack (Post-Exploit Analysis)**
- **Class:** Token Transfer Logic / Novel Attack Class
- **Chain:** BSC
- **Impact:** A filter removal in a protocol update enabled zero-value transferFrom calls to trigger unintended harvest logic on a liquidity pair, draining reserves. $377K+ USDT drained in the confirmed exploit. Classified as a novel vulnerability class.

---

### 🔴 High

**Stale Oracle Enables Flash Loan Drain**
- **Class:** Oracle Manipulation
- **Chain:** Base
- **Impact:** Missing timestamp validation on price feed allowed borrowing against inflated stale collateral prices, enabling full lending market drainage via flash loan in a single transaction.

**First Depositor Share Inflation Attack**
- **Class:** Math Precision (ERC4626 Inflation)
- **Chain:** Base
- **Impact:** Absence of minimum liquidity protection allowed first depositors to manipulate share price and steal subsequent depositors' full principal.

**Permanent Liquidation DoS via Division-by-Zero**
- **Class:** Math Precision / DoS
- **Chain:** Ethereum
- **Impact:** Outstanding bad debt with zero total units caused all liquidation calls to permanently revert, locking lender funds indefinitely.

**Permissionless Rebalance with Zero Effective Slippage Protection**
- **Class:** Oracle Manipulation / Slippage
- **Chain:** Base, Arbitrum, Polygon (7 live deployments)
- **Impact:** Unguarded rebalance function combined with manipulable slippage anchor enabled flash loan attacks draining up to 50% of TVL per transaction. Confirmed exploitable across all live deployments.

**Zero LP Supply Permanently Bricks Vault**
- **Class:** Math Precision / DoS
- **Chain:** Ethereum
- **Impact:** Missing zero guard on LP supply denominator caused permanent revert on all asset accounting calls, locking funds.

**Unrestricted Fee Parameter Drains Full ETH Float**
- **Class:** Access Control / Privilege Escalation
- **Chain:** EVM
- **Impact:** Caller-controlled fee and recipient parameters with zero validation allowed any permissioned relayer to drain the entire ETH balance in one transaction.

**Share Overflow Drains Beyond Protocol Profit**
- **Class:** Integer Overflow
- **Chain:** EVM
- **Impact:** Uint8 share parameter with a guard set above its maximum value allowed extraction of 155% beyond earned profit from vault float.

**Short TWAP Window Enables Undercollateralized Borrowing**
- **Class:** Oracle Manipulation
- **Chain:** HyperEVM
- **Impact:** 5-second TWAP window exploitable within 3 blocks on HyperEVM's ~2 second block time, enabling borrowing with far less collateral than fair value.

**No L1 Deposit Verification in Bridge Finalization**
- **Class:** Access Control
- **Chain:** Ethereum + Base
- **Impact:** Role-based authorization instead of canonical messenger verification allowed arbitrary NFT minting on L2 without corresponding L1 deposits under backend compromise.

**Cross-Decimal Scaling Collapse on Cross-Chain Transfers**
- **Class:** Math Precision
- **Chain:** Polkadot (Substrate/Rust)
- **Impact:** Saturating subtraction returning zero exponent silently collapsed all decimal scaling when local decimals exceeded ERC decimals, causing fund loss or inflation on cross-chain transfers.

**JIT Liquidity Removal DoS on All Hook-Triggered Swaps**
- **Class:** Hook Logic / DoS
- **Chain:** Base (Uniswap V4)
- **Impact:** Delta generated by JIT liquidity removal caused settlement revert on every subsequent swap through the hook, creating a systemic DoS. Confirmed as the hook's core expected behavior. Disclosed directly to the protocol team.

---

### 🟡 Medium (Selected)

> A representative sample. Full list available on request.

**Missing Slippage Protection in Hook Swap Path**
- **Class:** Slippage / MEV
- **Chain:** Base (Uniswap V4)
- **Impact:** No price slippage check before JIT-triggered swaps, exposing users to sandwich attacks. Confirmed valid by protocol team.

**Oracle Staleness Bypass via Zero staleTime**
- **Class:** Oracle / Input Validation
- **Impact:** Missing validation allowed feeds with staleTime=0 to accept arbitrarily old prices indefinitely.

**Division-by-Zero at Critical Collateralization Ratio**
- **Class:** Math Precision
- **Impact:** Exact 100% collateral ratio used as denominator, permanently bricking intervention logic at the most critical moment.

**Hardcoded Token Price Creates Overborrowing Risk**
- **Class:** Stale Hardcoded Value
- **Impact:** Price set at deployment with no update mechanism. Significant deviation between hardcoded and live price creates unchecked overborrowing exposure.

**Reward Rate Logic Error Enables Inflation**
- **Class:** Logic Error / Reward Inflation
- **Impact:** Loop overwrites config each iteration, allowing attacker to order claim list to maximise reward extraction beyond intended rate.

**Entire Leverage Product Non-Functional from Deployment**
- **Class:** Logic Error / DoS
- **Impact:** Mint logic caused price invariant to always fail, making the leverage function revert on every call since deployment.

**Strict Repayment Comparator Blocks Full Debt Closure**
- **Class:** Off-by-One
- **Impact:** Strict greater-than check prevented users from repaying exact debt amounts, forcing liquidation paths.

**Permit Frontrun DoS on Supply and Repay**
- **Class:** DoS
- **Impact:** Missing try/catch on permit call allowed frontrunners to permanently block victim supply and repay transactions.

**Co-signature Validation Not Implemented**
- **Class:** Access Control / Missing Validation
- **Impact:** Signature array decoded but never verified, leaving entire pool exposed to a single key compromise.

**Bridge Fee Bypass via Cross-Chain Routing**
- **Class:** Logic Error / Fee Bypass
- **Impact:** Fee flag disabled on destination chain when routed cross-chain, allowing complete swap fee bypass via bridge path.

**Lifetime Payout Cap Bypass via Claim-Reset**
- **Class:** Logic Error / Cap Bypass
- **Impact:** Earnings tracker reset on claim while lifetime counter went unenforced, allowing unlimited extraction beyond the intended 3x payout cap.

**EIP-712 Signature Replay Across Bridge Deployments**
- **Class:** Signature Replay
- **Impact:** Domain separator not bound to contract address, making valid withdrawal signatures replayable on any additional bridge deployment.

---

## Tools & Environment

| Category | Tools |
|----------|-------|
| Development | Foundry, Cast, Hardhat |
| Environment | GitHub Codespaces, VSCode, PowerShell, [Claude Code](https://claude.ai/referral/PFGhz1t0TA), [Claude Cowork](https://claude.ai/referral/PFGhz1t0TA?s=cowork&v=apps) |
| On-chain Research | Etherscan (+ chain equivalents), Dune Analytics, DexScreener, DeFiLlama API |
| Infrastructure | Alchemy RPC, multiple chain RPCs, block explorer APIs |
| Workflow | GitHub, grep/ripgrep, Glider |

---

## Chains & Languages

**Chains:** Ethereum, Base, Arbitrum, Avalanche, Polygon, BSC, Kaia, HyperEVM, Hyperliquid, TRON, Polkadot/Substrate

**Languages:** Solidity (speciality), Vyper, Rust (Substrate pallets)
