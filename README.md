# Ethena Minting V2 Nonce Truncation DoS

**High Severity Vulnerability Disclosure**

This repository contains a detailed security report on a deterministic permanent Denial-of-Service vulnerability in Ethena's canonical Mint and Redeem Contract V2.

## Summary

The `verifyNonce` function truncates `uint128` nonces to `uint64`, causing bitmap collisions for any nonce `N` and `N + 2^64`. 

Nonces in production have already reached 60 bits. Collisions are inevitable and will permanently brick mint/redeem for affected users with no on-chain recovery.

**Contract:** `0xe3490297a08d6fC8Da46Edb7B6142E4F461b62D3` (primary canonical V2)

**Severity:** High (DoS with no user workaround, requires contract migration to fix)

## Full Report

See [REPORT.md](./REPORT.md) for the complete disclosure including:

- Impact analysis
- Root cause
- Mainnet proof of collision
- Production nonce forensics (60-bit nonces already observed)
- Why "upgrade before 2028" is high-risk
- Recommended fix

## Reporter

**Tejanadh** — Independent Smart Contract Security Researcher

## Disclosure

This report was prepared for responsible disclosure. The vulnerability is deterministic once triggered and affects the primary production mint/redeem entrypoint for the Ethena protocol.

## Timeline

- **2026-05-27**: Initial disclosure published

## References

- Primary contract (Ethena docs): `0xe3490297a08d6fC8Da46Edb7B6142E4F461b62D3`
- Second affected deployment: `0x8a39215693aaB95038727fB31EBe19ce18903885`

---

*This repository is for security research and responsible disclosure purposes.*