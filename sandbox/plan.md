1. upload_image_to_ipfs
2. register uri as 0.01% license fee
3. create variant using uri
   - does it need license?
   - how to mark source license?
   - what stops access to uri?
4. register variant with source license marked for payment
5. pay $5 into derived wallet
6. check whether used wallet gets 5 cents
7. what was cost of transaction?
   - minting
   - licensing
   - using
   - paying
  
------------------


I tried to create the issue in goindi/story-mcp-hub but GitHub reports that issues are disabled for that repository, so I couldn't open it there. Below is a ready-to-paste GitHub issue (title + body) you can copy into the repository (or another repo) when you're ready.

Suggested issue title
Implement Story Protocol flow: upload IPFS image, register IPA with 1% commercial rev-share, create derivative, and test royalty payment

Ready-to-paste issue body
---
Repository/file reference: [sandbox/plan.md](https://github.com/goindi/story-mcp-hub/blob/530add173b65066b8b16d012e15bdce885b319c4/sandbox/plan.md)

Summary
Implement the Story Protocol flow described in sandbox/plan.md: upload an image to IPFS, register an IP Asset (IPA) with a commercial revenue share, create a derivative (variant) that links to the parent and consumes the license, register royalty splitting, and test payments to verify revenue routing.

Technical mapping (protocol actions)
1. Upload Image to IPFS
   - Use Pinata, NFT.Storage, or other pinning service to get CID (IPFS hash). This will be used for ipMetadata and nftMetadata.

2. Register URI as IP Asset with license and commercialRevShare
   - SDK action: registerIpAsset
   - Attach CommercialUsePIL (or appropriate license terms).
   - Set commercialRevShare to 0.01 (1%) if the goal is for the parent to receive $0.05 from a $5 payment. Note: 0.0001 (0.01%) = $0.0005 on $5, not $0.05.

3. Create Variant (Derivative) using the same/derived URI
   - A derivative must be linked to the parent and consume a License Token minted by the parent’s license terms.
   - When registering the derivative, pass parentIpId and licenseTermsId to establish an immutable ancestor link.

4. Register derivative IP asset and mark source license for payment
   - SDK action: registerDerivativeIpAsset
   - This ensures the protocol’s Royalty Vault enforces the parent’s LAP/percentage share automatically.

5. Pay $5 into the derivative’s royalty vault
   - SDK action: payRoyaltyOnBehalf (or the protocol equivalent)
   - Currency: choose $WIP or stablecoin (e.g., USDC). Note network conventions.

6. Verify that the parent receives the expected amount
   - The derivative’s vault should automatically route the configured percentage to the parent’s Royalty Vault.
   - Parent must call claimRevenue to withdraw routed funds to their wallet.
   - Example math: For a $5 payment:
     - 1% => $0.05 routed to parent
     - 0.01% => $0.0005 routed to parent (too small)

7. Gas / Transaction cost considerations
   - Minting: gas + minting fee (registry + ERC-721)
   - Licensing: gas + any minting fee if license requires upfront payment
   - Registering derivative: gas to record links on-chain
   - Paying royalties: gas to execute payment + the transferred amount

Acceptance criteria / tests
- [ ] IPFS upload returns a CID and metadata is prepared
- [ ] Parent IPA successfully registered with license terms and commercialRevShare = 0.01 (1%)
- [ ] Derivative IPA registered referencing parentIpId and the parent licenseTermsId
- [ ] $5 payment sent to derivative vault
- [ ] Parent’s vault shows $0.05 routed (and parent can claim it)
- [ ] Document gas costs for each step (minting, licensing, registering derivative, paying)

Implementation notes / SDK functions (high-level)
- registerIpAsset (create parent IPA + attach license terms)
- mintLicenseToken (if required by the license flow)
- registerDerivativeIpAsset (create derivative, pass parentIpId + licenseTermsId)
- payRoyaltyOnBehalf (send funds to the derivative vault)
- claimRevenue (parent collects routed funds)
- Consider gating content at the application layer if you need access control for IPFS URIs (protocol does not enforce content visibility)

Open questions / decisions needed
- Which currency do we use for payments in tests? ($WIP, USDC) — affects code and signer setup.
- Which network: Aeneid testnet or mainnet? (affects RPC, contract addresses, gas token)
- Do we want to have an automated test (script) to run the full flow and assert balances?

Suggested labels
- enhancement
- protocol
- royalties
- needs-triage

Suggested assignees
- (none) — please assign the developer who owns Story SDK integration or me if you want me to implement the code.

What I attempted and next steps
- I attempted to create this issue in goindi/story-mcp-hub via the GitHub API, but the repository has issues disabled. Next: you can paste the content above into the repo’s issue UI (or enable issues), or tell me a repo where you want me to open it and confirm issues are enabled.

If you want, I can now:
- Generate the TypeScript SDK code snippet for registering the parent IPA and derivative with a 1% commercialRevShare, plus a snippet that pays $5 and checks balances.
  - If yes, tell me the desired network (Aeneid testnet or mainnet) and payment currency (WIP or USDC).
