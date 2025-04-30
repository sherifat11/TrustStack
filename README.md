

# TrustStack - Service Agreement Smart Contract

## Overview

This Clarity smart contract implements a **Service Agreement System** on the Stacks blockchain, enabling trustless service engagements between a **client** and a **service provider**, with:

- 🔐 **Payment escrow**
- 📈 **Milestone-based service tracking**
- ⚖️ **Built-in dispute resolution**
- ❌ **Agreement termination controls**

---

## 📦 Features

- **Escrowed Payments**: Clients deposit funds that are locked until service delivery is confirmed.
- **Milestone Tracking**: Services are broken into 5 defined milestones, each with a payment value and completion status.
- **Dispute Resolution**: Either party can raise a dispute, which can be resolved by the contract administrator.
- **Termination Support**: Agreements can be terminated prior to payment, with escrowed funds refunded.

---

## ⚙️ Contract Constants

| Constant | Description |
|---------|-------------|
| `contract-administrator` | The deployer/administrator of the contract |
| `agreement-status-*` | Status codes for tracking agreement lifecycle |
| `ERROR_*` | Custom error codes for specific failure conditions |

---

## 🗂 Data Structures

### `service-agreement-details`
Tracks the full contract between client and service provider.

```clarity
{
  agreement-identifier: uint,
  service-provider-address: principal,
  client-address: principal,
  total-service-cost: uint,
  agreement-status: uint,
  agreement-start-block: uint,
  agreement-end-block: uint,
  dispute-filing-deadline-block: uint,
  service-milestones: list(5)
}
```

Each milestone includes:
- `milestone-description`: description of task
- `milestone-payment`: payment amount
- `milestone-completed`: whether milestone is done

---

### `agreement-payment-escrow`
Holds escrowed payment information.

```clarity
{
  escrowed-amount: uint
}
```

---

### `agreement-disputes`
Manages dispute details.

```clarity
{
  dispute-reason: string,
  dispute-initiator: principal,
  dispute-resolution: optional(string)
}
```

---

## 🔍 Read-Only Functions

| Function | Description |
|---------|-------------|
| `get-agreement-details(agreement-id)` | Returns agreement info |
| `get-escrowed-payment(agreement-id)` | Returns escrow balance |
| `get-dispute-details(agreement-id)` | Returns dispute details |

---

## 🛠 Public Functions

### 📄 Agreement Creation

#### `create-service-agreement(agreement-id, service-provider, total-cost, duration, milestones)`
Creates a new agreement with all milestones and terms defined.

---

### 💰 Payment Deposit

#### `deposit-payment(agreement-id, payment-amount)`
Client deposits funds into escrow. Marks agreement as active once full amount is received.

---

### ✅ Milestone Completion

#### `mark-milestone-complete(agreement-id, milestone-index)`
Service provider marks a specific milestone as complete.

---

### 🔓 Escrow Release

#### `release-escrowed-payment(agreement-id)`
Client releases funds to provider once service is marked delivered (all milestones complete).

---

### ⚔️ Dispute Management

#### `initiate-dispute(agreement-id, reason)`
Allows either party to initiate a dispute before the dispute deadline.

#### `resolve-dispute-claim(agreement-id, resolution, client-refund%)`
Admin resolves dispute by defining payout distribution between client and provider.

---

### ❌ Agreement Termination

#### `terminate-agreement(agreement-id)`
Allows either party to cancel agreement if it's still awaiting payment. Escrow (if any) is returned to client.

---

## ✅ Access Control Rules

| Action | Allowed Parties |
|--------|-----------------|
| Create agreement | Client |
| Deposit payment | Client |
| Mark milestone | Service Provider |
| Release funds | Client |
| Raise dispute | Client or Service Provider |
| Resolve dispute | Contract Administrator |
| Terminate agreement | Client or Service Provider (pre-payment only) |

---

## 🛡 Validations and Safeguards

- **No double creation**: Agreements are uniquely identified.
- **Authorized actions**: Only designated participants can act on agreements.
- **Milestone integrity**: All milestone payments must total the agreement value and have valid descriptions.
- **Dispute timing**: Disputes can only be raised before the specified block deadline.
- **Partial payments on dispute**: Admin can split funds with a client refund percentage (0–100%).

---
