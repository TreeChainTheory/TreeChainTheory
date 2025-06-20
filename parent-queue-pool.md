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

