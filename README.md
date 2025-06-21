# FINAL PROJECT MODULE 3

# SimpleSwap

A minimal, self‑contained automated market maker (AMM) that replicates the essential functionality of **Uniswap V2**—without importing or inheriting from the Uniswap codebase.  SimpleSwap lets liquidity providers stake ERC‑20 token pairs, enables traders to seamlessly exchange tokens, and exposes convenient helper methods for price discovery and output estimation.

> **Status:** *Reference implementation for educational purposes.* **Not audited—use at your own risk.**

---

## 📖 Smart Contracts

- **SimpleSwap.sol** – ERC‑20 contract that issues liquidity provider (LP) tokens and manages the pool. It allows anyone to add or remove liquidity, swap tokens and query pool prices.
- **RaulMedinaToken.sol** – ERC-20 token used to create tokens A and B. It has a simple minting function, restricted to the contract implementer.
- **SwapVerifier.sol** – Utility contract that validates a `SimpleSwap` deployment by exercising its functions. The verifier has been run and all tests pass, confirming `SimpleSwap` behaves as expected.

## ✨ Features

| Capability            | Description                                                                                                                          |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Add Liquidity**     | `addLiquidity()` Deposit two ERC‑20 tokens and receive LP tokens representing your share of the pool.  |
| **Remove Liquidity**  | `removeLiquidity()` Burn LP tokens to withdraw the underlying pair of tokens from the pool.            |
| **Swap Exact Tokens** | `swapExactTokensForTokens()` Swap a specific amount of one token for as much as possible of another token, subject to a minimum output amount. |
| **Get Price**  | `getPrice()` Returns the price of one token in terms of another, derived from current reserves.                             |
| **Get Amount**      | `getAmountOut()` Estimate how many output tokens will be received for a given input amount using the pool reserves.    |

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

## 🛠 Verification of correct operation

Using the verification contract `SwapVerifier` we check the correct operation of the developed contract `SimpleSwap`.

[Link to verification transaction details](https://sepolia.etherscan.io/tx/0xa3667e661e4580939bb040b7d2cc4cf5fa2f46bc7a52bd46187cf350c9f1bec2)

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
