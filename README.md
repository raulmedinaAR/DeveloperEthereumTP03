# FINAL PROJECT MODULE 3

# SimpleSwap

A minimal, self‑contained automated market maker (AMM) that replicates the essential functionality of **Uniswap V2**—without importing or inheriting from the Uniswap codebase.  SimpleSwap lets liquidity providers stake ERC‑20 token pairs, enables traders to seamlessly exchange tokens, and exposes convenient helper methods for price discovery and output estimation.

> **Status:** *Reference implementation for educational purposes.* **Not audited—use at your own risk.**

---

## ✨ Features

| Capability            | Description                                                                                                                          |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Add Liquidity**     | `addLiquidity()` transfers the provider’s tokens into the pool, mints liquidity tokens (LP tokens), and sends them to the provider.  |
| **Remove Liquidity**  | `removeLiquidity()` burns the caller’s LP tokens and returns the underlying token pair in proportion to current reserves.            |
| **Swap Exact Tokens** | `swapExactTokensForTokens()` executes a swap along a `path` of two tokens, guaranteeing the caller receives at least `amountOutMin`. |
| **On‑Chain Pricing**  | `getPrice()` returns the spot price of `tokenA` denominated in `tokenB`, derived from on‑chain reserves.                             |
| **Quote Helper**      | `getAmountOut()` is a pure helper that computes the expected output amount for an `amountIn`, given `reserveIn` and `reserveOut`.    |

---

## 🛠 Architecture

SimpleSwap maintains a **constant‑product invariant** (`x * y = k`) for every token pair, identical to Uniswap V2.  Liquidity providers deposit equal‑value amounts of `tokenA` and `tokenB` and receive LP tokens representing their share of the pool.

- **Reserves** are stored on‑chain and updated after every liquidity or swap operation.
- **Fees**: The reference implementation charges no protocol fee. Trading fee logic can be added by modifying the swap function.
- **LP Tokens**: Implemented as an internal ERC‑20, enabling composability with other protocols.

---

## 🔢 Math Primer

The core swap equation (ignoring fees) is:

```text
(x + Δx) · (y − Δy) = x · y = k
```

Solving for `Δy` gives the helper used in `getAmountOut()`:

```
amountOut = (amountIn * reserveOut) / (reserveIn + amountIn)
```

Slippage protection is enforced via the `amountAMin`, `amountBMin`, and `amountOutMin` parameters.

---

## 📖 Contract Interface

Below is a concise view of the public API.  NatSpec comments in the code provide full documentation.

```solidity
function addLiquidity(
    address tokenA,
    address tokenB,
    uint256 amountADesired,
    uint256 amountBDesired,
    uint256 amountAMin,
    uint256 amountBMin,
    address to,
    uint256 deadline
) external returns (uint256 amountA, uint256 amountB, uint256 liquidity);

function removeLiquidity(
    address tokenA,
    address tokenB,
    uint256 liquidity,
    uint256 amountAMin,
    uint256 amountBMin,
    address to,
    uint256 deadline
) external returns (uint256 amountA, uint256 amountB);

function swapExactTokensForTokens(
    uint256 amountIn,
    uint256 amountOutMin,
    address[] calldata path,
    address to,
    uint256 deadline
) external returns (uint256[] memory amounts);

function getPrice(
    address tokenA,
    address tokenB
) external view returns (uint256 price);

function getAmountOut(
    uint256 amountIn,
    uint256 reserveIn,
    uint256 reserveOut
) external pure returns (uint256 amountOut);
```

---

## 🔐 Security Considerations

| Risk                         | Mitigation                                                                            |
| ---------------------------- | ------------------------------------------------------------------------------------- |
| **Re‑entrancy**              | `nonReentrant` modifier (OpenZeppelin) recommended around state‑changing externals.   |
| **Slippage / Front‑running** | Users must supply `amount*Min` or `deadline` parameters to protect themselves.        |
| **Price Manipulation**       | On‑chain TWAP oracles are **not** included—do not rely on `getPrice()` in production. |
| **Unchecked Fees**           | No protocol fee is implemented; add trading fee logic carefully.                      |

---

## 📄 License

This project is released under the **MIT**.  See `LICENSE` for details.

---

## 🙏 Acknowledgements

- Inspired by the design of **Uniswap V2** (© Uniswap Labs) but implemented from scratch for learning purposes.
- OpenZeppelin Contracts for ERC‑20 templates.

---

## ✉️ Contact

Maintainer: **Raúl Medina** — feel free to open an issue or reach out at `raul@medina.net.ar` for questions or suggestions.
