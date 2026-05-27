# Ethena Minting V2 — Nonce Truncation Analysis

**Public Security Research**  
**Contract:** [0xe3490297a08d6fC8Da46Edb7B6142E4F461b62D3](https://etherscan.io/address/0xe3490297a08d6fC8Da46Edb7B6142E4F461b62D3) (Canonical Ethena Mint & Redeem V2)

## Summary

I analyzed a type truncation vulnerability in the `verifyNonce` function of Ethena's production minting contract. The function accepts `uint128` nonces but truncates them to `uint64` when calculating the bitmap deduplication slot.

This creates a latent structural vulnerability: any nonce whose lower 64 bits collide with a previously used nonce will result in a permanent `InvalidNonce` revert with no on-chain recovery path.

## Key Findings

- **Root Cause**: `uint64(nonce) >> 8` cast in `verifyNonce` (production V2)
- **On-Chain Verification**: `verifyNonce(addr, N)` and `verifyNonce(addr, N + 2^64)` return identical bitmap slot + bit on mainnet
- **Current State**: Observed nonces in production range from ~1.78e9 to 8.77e17 (60 bits). The 2^64 boundary has significant headroom under current patterns.
- **Impact**: Latent. Not currently exploitable at scale, but becomes a permanent per-user DoS the moment any flow generates nonces above the effective 64-bit boundary. No upper-bound enforcement exists in the contract.

## Why This Research Matters

This is a classic example of a "time bomb" class of bug in bitmap-based deduplication systems (similar patterns exist in Permit2 and other high-value protocols). The defect is simple, but the failure mode is severe and unrecoverable without a contract upgrade.

The analysis demonstrates:
- Live mainnet verification of smart contract behavior
- Careful impact assessment based on real transaction data rather than assumptions
- Responsible evaluation of severity (updated from initial High framing after on-chain investigation)

## Repository Contents

| File | Description |
|------|-------------|
| `technical-analysis.md` | Full technical report with on-chain evidence and root cause |
| `analysis-notes.md` | Detailed reasoning on impact framing and triage considerations |
| `test/NonceCollisionPoC.t.sol` | Foundry proof-of-concept (requires mainnet fork + correct storage slot) |
| `disclosure-email.md` | Original internal draft (for reference) |

## On-Chain Evidence

Real transaction data used in this analysis (May 2026):

- `0xb181c2a0e165d07c288aa51235fd6c1d9c28291f2053a9e1b8c0ef1e1d9ffd1e` — nonce `877108444170302954` (60 bits)
- Multiple other txs showing nonce distribution across different flows

Full verification commands and decoded data are included in the technical report.

## Proof of Concept

The PoC demonstrates two things:
1. Mathematical collision between `N` and `N + 2^64`
2. Actual DoS by simulating an early nonce being spent and showing the colliding future nonce reverts

**Note**: To run the DoS simulation test, you must determine the correct storage slot for `_orderBitmaps` in the deployed contract using `forge inspect` or storage layout analysis.

## Skills Demonstrated

- Smart contract vulnerability research
- On-chain verification and transaction decoding
- Careful severity assessment and impact framing
- Responsible disclosure practices
- Solidity storage layout understanding
- Foundry testing on mainnet forks

## Disclosure History

This research was initially explored as a potential bug bounty submission. After deeper on-chain investigation revealed lower immediate exploitability than initially modeled, the work was published publicly as technical research instead.

## Contact

For questions about the technical details or collaboration on similar research:

[Your contact / Twitter / Email]

---

*This repository contains original security research. All information is provided for educational and defensive purposes.*