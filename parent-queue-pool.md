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

#### 🧱 Genesis
```
  B
```

- ✅ PQP: `[B]`


#### 🧱 Level 1 – Children of B
```
    B
   / \
 L1   R1
```


- L1 and R1 are created from B.

- ✅ PQP: `[L1, R1]`

#### 🧱 Level 2 – Children of L1
```
      B
     / \
   L1   R1
  /  \
L11  L12
```

- L11 and L12 created from L1.

- ✅ PQP: `[R1, L11, L12]`

#### 🧱 Level 3 – Children of R1

```
          B
         / \
       L1   R1
      / \   / \
   L11 L12 R11 R12
```

- R11 and R12 created from R1.

- ✅ PQP: `[L11, L12, R11, R12]`


#### 🧱 Level 4 – Children of L11

```
          B
         / \
       L1   R1
      / \   / \
   L11 L12 R11 R12
  /   \
L111 L112
```


- L111 and L112 created from L11.

- ✅ PQP: `[L12, R11, R12, L111, L112]`


#### 🧱 Level 5 – Children of L12

```
              B
           /     \
         /         \
        L1          R1
      /    \       /   \
   L11       L12  R11   R12
  /   \     /   \
L111 L112  L121  L122 
```


- L121 and L122 created from L12.

- ✅ PQP: `[R11, R12, L111, L112, L121, L122]`

### ❌ `L11` is Found to be Malicious – Rollback Begins

- We now rollback all its descendants and repair the PQP.

#### 🔁 Step 1: Pop `L122` → Add parent `L12`

- ✅ PQP: `[L12, R11, R12, L111, L112, L121]`


#### 🔁 Step 2: Pop `L121` → `L12` already in PQP → skip adding

- ✅ PQP: `[L12, R11, R12, L111, L112]`


#### 🔁 Step 3: Pop `L112` → Add parent `L11` (malicious)

- `L11` is malicious → ❌ do **not** add to PQP

- ✅ PQP: `[L12, R11, R12, L111]`


#### 🔁 Step 4: Pop `L111` → `L11` already flagged malicious → skip

- ✅ PQP: `[L12, R11, R12]`

### ✅ Final Tree After Rollback

```
           B
         /   \
       L1     R1
         \   /  \
         L12 R11 R12
          
     L121  L122   ← removed

```


- `L11`, `L111`, and `L112` are removed.
- `L12` is now active and ready to continue growth.

#### 📌 Final PQP After Rollback

- ✅ PQP: `[L12, R11, R12]`


---


## ✅ TreeChain’s Structure Tolerates Partial Growth

- 🌳 **TreeChain is not strictly binary** — the 2-child model is just the ideal case.
- 🧩 **Parents are not required to have both children** at the same time.
- ❌ If a **child is invalidated**, the remaining child still stays valid and active.
- ✂️ Similar to **Merkle tree pruning** — we trim only the bad branch, not the whole structure.
- 🔁 The tree continues to grow from the valid parts without requiring full resets.
- 🛡️ So, L1 having only `L12` after `L11` is removed is **not a violation**, but a **resilient fallback**.

### 🔄 TreeChain vs Linear Blockchain in Fault Recovery

Let's compare how a malicious block affects both systems.

#### 🧱 TreeChain Example:

- Total blocks produced: 11
- Malicious block: L11 (4th level)
- Blocks removed: L11, L111, L112, L121, L122 → Total = 5
- Surviving blocks: 6 (L12, R1, R11, R12, B, L1)

✅ Recovery: Tree continues from L12 without restarting.


#### 🔗 Traditional Linear Blockchain:

- Total blocks produced: 11
- Malicious block: Block 4
- All blocks after Block 4 are invalid.
- Blocks removed: Blocks 4 to 11 → Total = 8
- Chain rolls back to Block 3 and **restarts**

❌ Recovery: Everything after the bad block is lost.


### ✅ Summary

| Model           | Malicious Block | Blocks Lost | Resumes From | Parallel Damage Limit |
|----------------|------------------|-------------|--------------|------------------------|
| TreeChain      | L11              | 5           | L12          | Only from bad subtree  |
| Linear Chain   | Block 4          | 8           | Block 3      | Entire rest of chain   |

TreeChain offers **localized damage recovery**, while linear chains require **global rollback**.
---
### ✨ Key Insight: Resilience of TreeChain

- ⚠️ Even when a block in **TreeChain** is found **malicious**...
- 🌳 ...the **tree doesn't collapse** like in traditional blockchains.
- 🔄 It **adapts and continues** from unaffected valid paths.
- 🚫 No need to discard the entire chain — only the affected subtree is pruned.
- ✅ This is the **core strength** of TreeChain over **linear blockchain models**.
---

### 👓 This design gives TreeChain:
- ✅ Fault recovery support
- ✅ Consensus flexibility
- ✅ Structural adaptability


