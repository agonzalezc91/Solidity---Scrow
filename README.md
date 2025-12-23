# Escrow Smart Contract (ETH) — Solidity

A minimal **Escrow** smart contract written in Solidity.  
It allows a **buyer** to deposit an exact amount of ETH into the contract, and later either:

- **release** the funds to the **seller** after confirming delivery, or
- **refund** the buyer if the deal is canceled (before completion).

This project is designed as a learning portfolio piece to practice Solidity fundamentals:
`msg.sender`, access control, `payable` functions, custom errors, events, and safe ETH transfers.

---

## ✅ Features

- Buyer-only deposit of an **exact ETH amount**
- Buyer-only **release()** to pay the seller
- Buyer-only **refund()** to return ETH to the buyer
- Simple state control using:
  - `funded` (deposit done)
  - `completed` (escrow finished)
- Uses **custom errors** for efficient reverts
- Emits events for each action
- Transfers ETH using low-level `.call` with success checks

---

## 👥 Roles

- **buyer**: the only address allowed to call `deposit()`, `release()` and `refund()`
- **seller**: receives the ETH once `release()` is called

The buyer and seller addresses are defined at deployment time through the constructor.

---

## 🔄 Contract Logic (State)

The contract uses two booleans:

- `funded`: becomes `true` after a successful deposit
- `completed`: becomes `true` after either `release()` or `refund()`

Rules:

- `deposit()` is allowed only if:
  - `funded == false`
  - `completed == false`
  - `msg.value == transactAmount`

- `release()` is allowed only if:
  - `funded == true`
  - `completed == false`

- `refund()` is allowed only if:
  - `funded == true`
  - `completed == false`

Once `completed` is true, the contract cannot be used again.

---

## 🧩 Contract Interface

### `deposit()` (payable)
Buyer deposits **exactly** `transactAmount` wei into the contract.

### `release()`
Buyer confirms delivery and releases the locked ETH to the seller.

### `refund()`
Buyer cancels the deal and receives the locked ETH back.

---

## 📣 Events

- `Deposited(buyer, amount)`
- `Released(seller, amount)`
- `Refunded(buyer, amount)`

Events can be checked in Remix logs after each transaction.

---

## 🧪 How to Test in Remix (Step-by-step)

1. Open Remix: https://remix.ethereum.org
2. Create a file called `Escrow.sol` and paste the contract code
3. Compile with Solidity version **0.8.24**
4. Deploy the contract:
   - `buyer_`: choose one Remix account as buyer
   - `seller_`: choose another Remix account as seller
   - `transactAmount_`: set the amount in wei  
     Example: `1000000000000000000` = **1 ETH**

### Test flow

**A) Deposit**
- Switch Remix account to the **buyer**
- Call `deposit()` and send **exactly** `transactAmount_` wei

**B) Release to seller**
- Still as buyer, call `release()`
- Check seller balance increases
- Check `Released` event in logs

**C) Refund to buyer**
- Deploy again (fresh contract)
- Buyer calls `deposit()`
- Buyer calls `refund()`
- Check buyer balance increases
- Check `Refunded` event in logs

---

## ⚠️ Notes

- This contract is intentionally minimal and intended for **learning purposes**.
- There is **no arbiter** role for disputes; only the buyer can finalize the escrow.
- ETH transfers are done with `.call{value: ...}("")` and reverted if the transfer fails.

---

## 🚀 Possible Improvements

- Add an **arbiter** role for dispute resolution
- Add `receive()` to allow direct ETH transfers (MetaMask “Send”)
- Add deadlines (timestamps) to enforce time-based refunds
- Add unit tests using Foundry
- Support partial payments or multiple milestones

---

## 👤 Author

Personal learning project for building Solidity and Web3 development skills.
