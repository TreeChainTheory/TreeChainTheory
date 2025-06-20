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


