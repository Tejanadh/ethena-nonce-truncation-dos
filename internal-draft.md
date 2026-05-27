**Subject:** [HIGH] Nonce Truncation in Ethena Minting V2 Causes Permanent DoS of Core Mint/Redeem (Mid-Late 2028) — Affects Canonical Production Contract

Dear Immunefi Team / Ethena Security,

I am reporting a High severity vulnerability affecting Ethena's live production minting infrastructure.

### Summary

The `verifyNonce` function in the canonical Minting V2 contract (`0xe3490297a08d6fC8Da46Edb7B6142E4F461b62D3`, officially documented and live-routed via Ethena API) truncates `uint128` nonces to `uint64`. This creates deterministic collisions once timestamp-derived nonces cross 2^64 (mid-to-late 2028).

- Primary canonical V2 contract + a second deployed contract with identical logic are both affected.
- Once triggered: permanent `InvalidNonce()` reverts on all mint and redeem calls.
- No on-chain mitigation exists. Backend changes are insufficient.

### Impact Classification

High (not Critical). Meets the bar for a significant, deterministic DoS of the protocol's core user-facing function with no simple remediation path.

### Verification Status

- Confirmed live on mainnet via direct `eth_call` (exact same return data for nonce `N` and `N + 2^64`).
- Official Ethena documentation and live API responses name the primary address as the active minting contract.
- ~$95M collateral context + 26k+ historical transactions confirm production status.
- Previous V1→V2 migration precedent demonstrates that contract upgrades are heavyweight, multi-party operations — not a trivial "just upgrade" fix.

Full technical report, on-chain proofs, and Foundry PoC (covering both contracts) attached.

Happy to provide any additional data or coordinate on disclosure timeline.

Best regards,  
[Your Name / Handle]  
[Immunefi username]
