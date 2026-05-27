## Research Notes: Severity & Impact Reasoning

**Challenge 1: "This is far away / low likelihood, they can mitigate by upgrading."**

> Real mainnet data shows nonces already reaching 60 bits (e.g. 8.77 × 10^17 in tx 0xb181c2a0...). The truncation means any future nonce sharing the same lower 64 bits as a previously used one will collide permanently. Ethena's own V1→V2 migration required multi-stakeholder coordination, institutional buy-in, and was scheduled weeks in advance. The current contract holds ~$95M in collateral, has whitelisted custodians, active roles, and routes live API traffic. A safe migration requires re-whitelisting every custodian, re-granting every role, coordinating with every institutional participant, auditing the new contract, and migrating collateral — all without disrupting a live protocol. The code change is one line; the operational cost is a full protocol-scale upgrade. The cost of inaction is permanent bricking of specific nonces for affected users with no on-chain recovery.

---

**Challenge 2: "The backend can just cap nonces below 2^64."**

> The contract has no on-chain enforcement of nonce range. Any whitelisted user can submit a signed order with a nonce above 2^64 — the contract accepts it and processes the collision silently. Backend changes are not a contract-level fix. The vulnerability lives in the contract, the fix must live in the contract.

---

**Challenge 3: "This is a DoS, not fund loss, so it's Medium not High."**

> Immunefi's own severity framework classifies permanent freezing of protocol functionality as High. When nonces cross 2^64, every affected user's mint and redeem calls revert permanently with no recovery path on the current contract. USDe holders cannot redeem collateral. That is permanent freezing of the core protocol interaction for all active users — not a temporary or partial disruption.

---

**Challenge 4: "Only one contract is affected."**

> This report covers the canonical production V2 Mint and Redeem contract at `0xe3490297a08d6fC8Da46Edb7B6142E4F461b62D3`, explicitly named in Ethena's official documentation and live API responses as the active minting contract. The truncation bug and collision behavior were confirmed directly via mainnet `eth_call` on this contract.
