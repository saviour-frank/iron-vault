# 🏦 **IronVault Protocol**

### *Bitcoin-Secured Credit System Built on Stacks*

---

## 📘 Overview

**IronVault** is a **Bitcoin-collateralized credit protocol** built on the **Stacks blockchain**, leveraging Bitcoin’s proof-of-work security to provide **trust-minimized, overcollateralized lending**.

The protocol enables users to:

* **Lock BTC** as collateral on-chain.
* **Borrow liquidity** without giving up Bitcoin exposure.
* **Maintain decentralized and transparent control** over collateralized debt positions (CDPs).

IronVault combines the **immutability of Bitcoin** with the **programmability of Stacks**, offering a fully on-chain system for credit issuance, collateral management, and liquidation enforcement.

---

## ⚙️ System Overview

IronVault’s lifecycle revolves around three core operations:

1. **Collateral Deposit**
   Users lock BTC-equivalent value into the protocol to establish a collateral base.

2. **Loan Request & Issuance**
   Borrowers mint debt positions against their BTC collateral, subject to a **minimum collateral ratio (150%)** and a **liquidation threshold (120%)**.

3. **Repayment or Liquidation**

   * Borrowers repay loans plus accrued interest to reclaim BTC collateral.
   * If collateral value falls below the liquidation threshold, the position is automatically liquidated.

The system is **governed by a contract owner (DAO or multisig)** who can:

* Update collateral ratios and liquidation thresholds.
* Manage price feeds for supported assets (e.g., BTC/STX).

---

## 🧱 Contract Architecture

### **1. Core Constants & Governance Variables**

| Variable                   | Description                                                   |
| -------------------------- | ------------------------------------------------------------- |
| `CONTRACT-OWNER`           | Authorized administrator (typically DAO or protocol multisig) |
| `minimum-collateral-ratio` | Required overcollateralization percentage (default: 150%)     |
| `liquidation-threshold`    | Trigger ratio for liquidation (default: 120%)                 |
| `platform-fee-rate`        | Protocol fee on loan issuance (default: 1%)                   |

---

### **2. Data Maps**

| Map                 | Purpose                                                                        |
| ------------------- | ------------------------------------------------------------------------------ |
| `loans`             | Tracks each loan’s metadata — borrower, collateral, amount, rate, status, etc. |
| `user-loans`        | Maps users to their active loan IDs.                                           |
| `collateral-prices` | Maintains on-chain BTC and STX price feed data.                                |

---

### **3. Key Public Entry Points**

| Function                                      | Description                            |
| --------------------------------------------- | -------------------------------------- |
| `initialize-platform`                         | One-time setup by contract owner.      |
| `deposit-collateral(amount)`                  | Deposit BTC collateral into protocol.  |
| `request-loan(collateral, loan-amount)`       | Create a new overcollateralized loan.  |
| `repay-loan(loan-id, amount)`                 | Repay outstanding loan and interest.   |
| `update-collateral-ratio(new-ratio)`          | Adjust system collateral requirements. |
| `update-liquidation-threshold(new-threshold)` | Modify liquidation sensitivity.        |
| `update-price-feed(asset, new-price)`         | Update oracle-provided asset prices.   |

---

### **4. Private Helper Logic**

| Helper                         | Role                                                                        |
| ------------------------------ | --------------------------------------------------------------------------- |
| `calculate-collateral-ratio()` | Determines collateralization percentage of a loan.                          |
| `calculate-interest()`         | Computes accrued interest based on elapsed blocks.                          |
| `check-liquidation()`          | Verifies if loan is undercollateralized and triggers liquidation if needed. |
| `liquidate-position()`         | Marks loan as liquidated and removes user association.                      |

---

## 🔄 Data Flow

```mermaid
flowchart TD

A[User] -->|Deposit BTC| B[deposit-collateral()]
B -->|Update Total Collateral| C[(total-btc-locked)]

A -->|Request Loan| D[request-loan()]
D -->|Verify BTC Price & Collateral Ratio| E[collateral-prices]
D -->|Create Loan Record| F[(loans Map)]
D -->|Register User Loan| G[(user-loans Map)]

A -->|Repay Loan| H[repay-loan()]
H -->|Compute Interest| I[calculate-interest()]
H -->|Update Loan Status| F
H -->|Release Collateral| C

subgraph Governance
J[update-price-feed()] --> E
K[update-collateral-ratio()] --> D
L[update-liquidation-threshold()] --> D
end
```

---

## 🧩 System Components

| Component              | Description                                                            |
| ---------------------- | ---------------------------------------------------------------------- |
| **Loan Engine**        | Core logic for loan creation, repayment, and tracking.                 |
| **Collateral Manager** | Maintains collateral balances, ratio checks, and liquidation triggers. |
| **Oracle Interface**   | Enables secure price feed updates for BTC and other supported assets.  |
| **Governance Module**  | Restricts administrative control to contract owner (DAO/multisig).     |

---

## 🛡️ Security & Risk Mitigation

* **Overcollateralization**: Ensures solvency under volatile market conditions.
* **Immutable Ledger**: Loan data and liquidation events are transparent and tamper-proof.
* **Strict Input Validation**: Prevents invalid asset, loan, or price feed operations.
* **Authorized Governance**: Only contract owner can modify system parameters.

---

## 🧠 Example Workflow

1. **Initialize Platform**

   ```clarity
   (contract-call? .ironvault initialize-platform)
   ```

2. **Deposit Collateral**

   ```clarity
   (contract-call? .ironvault deposit-collateral u1000000)
   ```

3. **Request Loan**

   ```clarity
   (contract-call? .ironvault request-loan u1000000 u500000)
   ```

4. **Repay Loan**

   ```clarity
   (contract-call? .ironvault repay-loan u1 u505000)
   ```

---

## 🔍 Read-Only Queries

| Function                    | Description                                    |
| --------------------------- | ---------------------------------------------- |
| `get-loan-details(loan-id)` | Fetch details of a specific loan.              |
| `get-user-loans(user)`      | Retrieve a user’s active loan list.            |
| `get-platform-stats()`      | Get global protocol statistics and parameters. |
| `get-valid-assets()`        | Return supported collateral assets.            |

---

## 📜 License

MIT License © 2025 IronVault Protocol Contributors.
