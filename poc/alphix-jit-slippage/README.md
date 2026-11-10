# PoC — Alphix JIT Liquidity: Missing Price-Slippage Protection (Base, Uniswap V4 hook)

Foundry mainnet-fork proof for the **Medium** finding in the portfolio:
*"Missing Slippage Protection in Hook Swap Path"* — confirmed by the Alphix team by email
(the hook was already deprecated/removed from the interface at report time, which is why
the exact contract is published here: there is no live user to protect).

## What the finding is

`_computeBeforeSwapJit` reads the live `sqrtPriceX96` from `poolManager.getSlot0` and sizes
JIT liquidity from it **without** any TWAP/deviation check. The same contract's
`addReHypothecatedLiquidity` / `removeReHypothecatedLiquidity` paths *do* call
`_checkPriceSlippage` — the JIT path omits it. A flash-loan sandwich can therefore move
`sqrtPriceX96` before `beforeSwap` fires and make the hook compute JIT liquidity at a
manipulated ratio.

## What this PoC proves (and what it does not)

- **Proves:** on a live Base fork, a 100 USDC → ETH swap routes through the Alphix hook and
  triggers the full unguarded JIT path — `modifyLiquidity` add, `modifyLiquidity` remove,
  `withdrawETH` from the Aave yield source, `settle` — driven entirely by the unprotected
  live `sqrtPriceX96`. See `test/exploit/poc_trace.txt`.
- **Does not claim a completed drain.** On current live state the swap ultimately reverts with
  `CurrencyNotSettled` (an unresolved ~100e6 USDC delta) — a *separate* DoS behaviour the team
  classified as expected for that deprecated hook. The slippage gap becomes an active
  extraction path only once that settlement behaviour is fixed. The PoC is scoped honestly to
  the slippage finding, not to a live profit.

## Files

| File | Role |
|---|---|
| `test/exploit/AlphixJITPocTest.t.sol` | Main test — swaps through the hook, logs yield-source state before/after, shows the JIT path executing. `[PASS]`. |
| `test/exploit/AlphixReadTest.t.sol` | Reads live pool + hook state (slot0, liquidity, yield-source balances). |
| `test/exploit/AlphixTest.t.sol` | Confirms the live `PoolKey` → `PoolId` mapping. |
| `src/exploit/AlphixJITAttacker.sol` | Flash-loan sandwich contract (two-swap manipulation) demonstrating the intended attack shape against the unprotected JIT sizing. |
| `test/exploit/poc_trace.txt` | Captured `forge test -vvvv` trace of the passing run. |

## Running

Requires a Uniswap V4 Foundry project with `v4-core` remapped and a `base` RPC endpoint alias.
Drop these files into that project's `src/` and `test/`, then:

```bash
forge test --mp test/exploit/AlphixJITPocTest.t.sol -vvvv
```

Target (Base): hook `0x831CfDf7c0E194f5369f204b3DD2481B843d60c0`,
PoolManager `0x498581fF718922c3f8e6A244956aF099B2652b2b`,
USDC `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913`.
