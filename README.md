# clone-lin-erc4626-vault

**EXPERIMENTAL** LIN clone of OpenZeppelin [`ERC4626._convertToShares` / `_convertToAssets`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.2/contracts/token/ERC20/extensions/ERC4626.sol) (MIT upstream).

This is **not** a vault, not a Yearn/ERC-4626 replacement, and not uint256. The kernel is uint64-scale with a 128-bit product for `(assets * (supply + 10**offset)) / (totalAssets + 1)`. Class: EXPERIMENTAL.

## Provenance

| Field | Value |
|---|---|
| Upstream | https://github.com/OpenZeppelin/openzeppelin-contracts |
| Path | `contracts/token/ERC20/extensions/ERC4626.sol` |
| Tag | `v5.0.2` |
| Commit | `dbb6104ce834628e473d2173bbc9d47f81a9eec3` |
| File sha256 | `bc3eb01a42e08f33e8591a726e28c7107b6c4fedd6be0d9d2d1c049853698be8` |
| Git blob | `ec6087231cb5e9a8c9a91d3eec8583fb89e1e168` |
| License (upstream) | MIT |
| Formula (Floor) | `assets * (totalSupply + 10**offset) / (totalAssets + 1)` |

Canonical vectors (recomputable): empty vault `previewDeposit(1000)` with offset=0 → 1000 shares; after 1 share and 1e12 donated assets, `previewDeposit(1e12)` → 1 share.

## Files

- `src/lin_erc4626_vault.lin` — scalar LIN kernel (128-bit schoolbook muldiv + virtual offset)
- `test/erc4626_scalar.c` — pointer-free C11 pow10/virtuals helpers (eligible for `lin_from_c`)
- `docs/PROVENANCE.rulel` — claims / non-claims

## Proofs live in lin-open

Results and the external harness stay in the LIN toolchain repo:

- Results: https://github.com/kbelludoo/lin-open/tree/cursor/linguagem-lin-e-valida-o-e4ba/examples/erc4626_vault
- Harness: `python3 test/prove_erc4626_vault_external.py` (gcc == Python == `lin_c0` vm)
- Claim sheet: `docs/events/EVENT_ERC4626_VAULT_CLONE_LIN.rulel`

```bash
make -C transpile/c c0
./transpile/c/bin/lin_c0 vm src/lin_erc4626_vault.lin e4626_test_suite
# value=1
./transpile/c/bin/lin_c0 vm src/lin_erc4626_vault.lin e4626_preview_deposit 1000 0 0 0
# value=1000
./transpile/c/bin/lin_c0 vm src/lin_erc4626_vault.lin e4626_shares_floor 1000000000000 1 1000000000000 0
# value=1
```
