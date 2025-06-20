## 🧾 Parent Queue Pool (PQP)

The **Parent Queue Pool** is a decentralized **double-ended queue** (deque) that maintains a list of upcoming parent blocks. It is a core component of the TreeChainTheory protocol, enabling scalable and parallel block generation.

### 🔁 What does the PQP do?

- 🧠 Tracks all blocks that are eligible to become parents.
- 🔄 Manages parent assignment for future child blocks.
- 🚮 Removes a parent block once it gets its designated number of children (2 in the ideal case).

### 📦 What does each PQP entry contain?

Each block that is added to the treechain creates a new entry in the PQP. These entries contain metadata necessary for verification and scheduling:

```json
"pqp_entry": {
  "queue_index": N,
  "block_hash": "<hash of the new block>",
  "parent_hash": "<hash of the parent block>",
  "leader_address": "<public key or address of the leader who created the block>",
  "signature": "<signed by the leader to prove authenticity>"
}
```
---
## 🧩 PQP Entry Structure

Each entry in the **Parent Queue Pool (PQP)** contains essential metadata about blocks that will act as parents in the TreeChain.

- 🆔 **queue_index**: Position in the PQP (sequential, deterministic).
- 🔗 **block_hash**: Unique hash of the newly created block.
- 🌳 **parent_hash**: Hash of the parent block from which this block was derived.
- 👤 **leader_address**: Identity/public key of the block-producing leader.
- ✍️ **signature**: Digital signature by the leader proving authorship and integrity.

---

## 🧱 How is the PQP Updated?

In **TreeChain**, the PQP is updated in a decentralized and verifiable way by embedding the entry **directly inside the block**.

- 📦 The `pqp_entry` is included in the block body at the time of block creation.
- 🌐 All nodes extract the `pqp_entry` upon receiving the block.
- 📋 Each node appends the entry to their **local PQP** based on queue rules.
- ✅ The block itself serves as **cryptographic proof** of the PQP entry.

---

## ✅ Why Embed PQP Inside the Block?

- 🔒 **Integrity**: If the block is valid, its PQP entry is inherently valid.
- 🧩 **Simplicity**: No extra gossip or sync mechanism is needed for the queue.
- ⚙️ **Consistency**: All nodes see the same PQP if they agree on the TreeChain.

---

## 🔄 Alternative Design: Gossip-Based PQP (Advanced)

A more modular design is possible for future versions of TreeChain:

- 📡 Leaders broadcast PQP entries **separately** after block creation.
- 🤖 Network nodes validate and append entries via a **gossip protocol**.
- ❗ Requires **conflict resolution rules** for duplicated or invalid entries.
- 🧪 Useful for large-scale networks or upgrades needing **flexible PQP consensus**.

> 💡 This approach trades simplicity for greater control and decoupling — and is ideal for advanced implementations of TreeChain.


## 🔁 Why is the Parent Queue Pool (PQP) a Double-Ended Queue?

Although TreeChain typically **adds entries at the end** and **removes from the front**, the Parent Queue Pool (PQP) is intentionally implemented as a **double-ended queue (deque)** to support rollback, error correction, and flexible consensus mechanisms.

### 🧨 When Is the Back Used for Removal?

- ✅ New `pqp_entry` blocks are added to the **back** (right side).
- ✅ Parents are removed from the **front** (left side) once they produce two children.
- ❗ If a **block is found malicious or invalid**, TreeChain must **rollback that branch**.
- 🔄 The rollback process **removes children from the back**, and for every child removed:
  - Its **parent is added to the front** of the PQP.
- 🧠 This enables TreeChain to re-assign trustworthy parents for future block creation.

### 🧩 PQP Entry Structure

- 🆔 **queue_index**: Position in the PQP (sequential and unique).
- 🔗 **block_hash**: Hash of the new block that becomes a parent.
- 🌳 **parent_hash**: Hash of the block’s parent.
- 👤 **leader_address**: Address or public key of the block-producing leader.
- ✍️ **signature**: Signature by the leader, proving authorship.

### 🧩 Rollback Rule Summary

- 🔄 If any block is removed from the back:
  - Its **parent** is **re-added to the front**.
  - Ofcourse if the **parent** is already in the **pqp** then it wont get added.
- 🔁 This process repeats until the invalid block is removed.
- ✅ Ensures no valid branch is permanently lost due to one faulty child.

### 🔒 If its a Confirmed-Only Consensus Variant

> Only blocks that are confirmed or finalized are allowed to enter the PQP, so this problem won't arise.

- 🚫 Prevents unconfirmed blocks from ever entering the tree.
- 🧱 Guarantees only stable, trusted blocks can become parents.
- ⚙️ Eliminates the need for rollback mechanisms entirely.

### 📌 Why Double-Ended Matters

- ⬅️ **Left side (front)**: Used to re-add safe parent blocks during rollbacks.
- ➡️ **Right side (back)**: Used to append newly created child blocks.

---

### 🌳 Example 1: Rolling Back a Single Malicious Block

- Initial Tree Structure:
```
   B1
  /  \
B2   B3 ← Malicious Block

```

- PQP before rollback:
  - PQP → [B2, B3]

- Rollback steps:
- ❌ Remove B3 from the back (invalid block)
- 🔁 Add B1 (its parent) to the front

- PQP after rollback:
  - PQP → [B1, B2]

---
### 🌲 Example 2: Rolling Back a Deep Subtree

- Initial Tree Structure:

---

### 👓 This design gives TreeChain:
- ✅ Fault recovery support
- ✅ Consensus flexibility
- ✅ Structural adaptability


