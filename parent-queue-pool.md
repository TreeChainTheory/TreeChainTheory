# 🧾 Parent Queue Pool (PQP)

The **Parent Queue Pool** is a decentralized **queue** (deque) that maintains a list of upcoming parent blocks. It is a core component of the TreeChainTheory protocol, enabling scalable and parallel block generation.

### 🔁 What does the PQP do?

- 🧠 Tracks all blocks that are eligible to become parents.
- 🔄 Manages parent assignment for future child blocks.
- 🚮 Removes a parent block once it gets its designated number of children (N).

---
### 📦 What does each PQP entry contain?

Each block added to the TreeChain includes a compact **`pqp_entry`** —  
a record of essential parent-related metadata embedded within the block itself. 
<details>
  <summary>click to view pqp_entry in the block:</summary>
  
    ```json  
    "pqp_entry": {
      "queue_index": N,
      "leader_address": "<public key or address of the leader who created the block>",
      "prev_pqp_commitment" : "<points to the pqp commitment of the same aligned previous block>",
      "signature": "<signed by the leader to prove authenticity>"
    }
    ```
</details>
More feilds are extracted from the block and made such an entry (just like the below one) that helps link the block into the broader **Parent Queue Pool (PQP)** system.

```json
"pqp_entry": {
  "queue_index": N,
  "align": "<alignment level of the block>",
  "block_hash": "<hash of the new block>",
  "parent_hash": "<hash of the parent block>",
  "pqp_commitment": "<commitment hash of internal fields (includes block_hash)>",
  "prev_pqp_commitment": "<link to previous aligned block>",
  "leader_address": "<public key/address of the block-producing leader>",
  "signature": "<signed by the leader to prove authenticity>"
}
```
- The pqp_entry remains within the block, making every block self-verifiable and self-contained.
- It defines both structural position (through `queue_index` and `align`) and cryptographic linkage (through commitment fields).
---
### 🧩 Parent Queue Entry Structure 

- A `queue` of these entries make the **PQP**.
When a new block is created, a separate record — called the **`ParentQueueEntry`** —  
is generated and added to the **Parent Queue Pool (PQP)**.  
This entry **extracts and extends** fields from the block’s `pqp_entry`.

- 🆔 **queue_index** → Position of the block in the PQP sequence.  
- 🧭 **align** → Alignment level of the block (1-aligned, 2-aligned, …).  
- 🧱 **block_hash** → Hash of the block (acts as unique ID).  
- 🌳 **parent_hash** → Hash of the block’s parent.  
- 🔐 **pqp_commitment** → Commitment hash of internal block metadata.  
- 🔗 **prev_pqp_commitment** → Points to the previous block in the same alignment path.  
- 👤 **leader_address** → Public key or address of the block creator.  
- ✍️ **signature** → Signature proving authenticity of the leader.  
- ⚙️ **status** → Current PQP state (e.g., `eligible`, `completed`, `removed`).  
> If the entry is removed after getting enough children from the PQP , then status feild is not needed

The **ParentQueueEntry** is extracted from the `block` & is not the same as `pqp_entry` and exits only in the PQP memory/state — and is dynamically updated as the tree expands.


---

## 🧱 How is the PQP Updated?

In **TreeChain**, the PQP is updated in a decentralized and verifiable way by embedding the entry **directly inside the block**.

- 📦 The `pqp_entry` is included in the block body at the time of block creation.
- 🌐 All nodes extract the `pqp_entry` with `additional required feilds` upon receiving the block.
- 📋 Each node appends the entry to their **PQP** based on queue rules.
- ✅ The block itself serves as **cryptographic proof** of the PQP entry.

---

## ✅ Why Embed PQP Inside the Block?

- 🔒 **Integrity**: If the block is valid, its PQP entry is inherently valid.
- 🧩 **Simplicity**: No extra gossip or sync mechanism is needed for the queue.
- ⚙️ **Consistency**: All nodes see the same PQP if they agree on the TreeChain.

---
# ⛓ PQP Workflow

- **`PQP`** holds all `ParentQueueEntry` instances inside its **`pool`**, which behaves as a **double-ended queue**.

- When a **new block** is created:
  - Its **PQP entry** (with additional metadata) is **added to the end** of the PQP pool.  
  - Initially, the PQP starts with only the **genesis entry**.  
  - The **first entry** in the PQP pool represents the **current parent**.  
  - The **second entry** represents the **next parent**.

- **Parent update conditions:**
  - When the **current parent** receives the maximum number of **`CHILDREN`** blocks (all pointing to its `block_hash`),then it is **discarded**.  
  - If the **next parent** receives **any child block** (whose `parent_hash` equals the next parent’s `block_hash`):  
    - The **current parent** is **discarded**, and  
    - The **next parent** becomes the new **current parent**.

- **While creating a new block:**
  - Miner/Leader uses the **current parent’s `block_hash`** as its **`parent_hash`**.  
  - To determine **`prev_pqp_commitment`**:
    - It **iterates from the back** of the PQP pool, searching for an entry with the **same alignment**.  
    - If not found, it queries the **TreeChain (IndexMap)** starting from the **first entry’s `queue_index` → 0**,  
      until it finds a block of the same alignment.  
    - If found, it takes that block’s **`pqp_commitment`** as the **`prev_pqp_commitment`**.  
    - If no such aligned block exists, it defaults to the **genesis commitment**.

- **This mechanism ensures:**
  - **Order-preserving linkage** between aligned blocks.  
  - **Efficient parent updates** as the tree grows.  
  - **Continuous hashing** across all alignment levels — maintaining both **tree structure** and **sequential security**.
---
# 🌴 TreeChain & ⛓ PQP ( Visualization )
- Let this **Visualizaton** follow **POW** model.

### Genisis Child Propagation (CHILDREN = 3)
<img width="450" height="300" alt="image" src="https://github.com/user-attachments/assets/d6b3b6e9-7018-4821-9f66-ccc8f4759316" />

- Initially There will be genesis in both Tree and PQPool acting as Parent and Previous Pqp commitment at the same tiem.
- When Some Node entered the **TREE** its immediately added to the **PQP**
- As soon as **Node 0** receives 3 children it is removed from the **PQPool**.

### 1st Node Parent
<img width="500" height="300" alt="image" src="https://github.com/user-attachments/assets/b601c0b8-647e-4726-a3c3-8fd12ab42cb8" />

- Each Node is supplies 2 feilds for next blocks , 1.**Parent Hash** , 2.**PQP commitment** .
- The **PQP** handles the order of blocks to be **parents**

### Next Prent getting Children earlier
<img width="600" height="341" alt="image" src="https://github.com/user-attachments/assets/a0c34da4-33b2-41b2-a280-e46780fd52c0" />

- Here **2** is **current parent** , but even before the 3rd aligned child is mined , 1st aligned child of **next parent** got mined. so the **next parent now becomes the current parent**.
- The current parent is now **removed** from the **PQPool**
- You can see the **queue_index** is preserved in the bfs left to right ordering even if a child is missed.

> **queue_index**: In a K-ary tree (e.g., a 3-ary tree, here K = CHILDREN), if all possible node positions are numbered sequentially from the genesis node (0) to the last node, from left to right and level by level, then each block’s queue_index equals the index of its intended position — even if one or more earlier positions (blocks) are missing.

### Prev PQP Commitment connection
<img width="600" height="381" alt="image" src="https://github.com/user-attachments/assets/27734001-a64b-4278-a6c3-1f111f69e78e" />


- The **PQP Commitment** connection ensures the chain continuity among different **aligned blocks** as shown in the diagram
- Its clear that the Previous PQP commitment is connected to the same aligned previous block in the **TREE**

---

**Why PQP Commitment Connection is Needed**
 - Ensures **hash continuity** among different aligned blocks in the TreeChain.  
 - Without this connection, when a block is mined, its hash is **not continued** until it becomes a parent block.  
 - This breaks the cryptographic linkage (unlike Bitcoin’s continuous block hash chain).  
 - Lack of continuity makes it **easier for attackers** to manipulate or rewrite parts of the TreeChain.

---

## 🔄 Alternative Design: Gossip-Based PQP (Advanced)

A more modular design is possible for future versions of TreeChain:

- 📡 Leaders broadcast PQP entries **separately** after block creation.
- 🤖 Network nodes validate and append entries via a **gossip protocol**.
- ❗ Requires **conflict resolution rules** for duplicated or invalid entries.
- 🧪 Useful for large-scale networks or upgrades needing **flexible PQP consensus**.

> 💡 This approach trades simplicity for greater control and decoupling — and is ideal for advanced implementations of TreeChain.

---

## 🔁 Why is the Parent Queue Pool (PQP) a Queue?

The **Parent Queue Pool (PQP)** is fundamentally designed as a **queue**,  
where new parent entries are appended in sequence as the tree expands.
and once a parent receives the

However, it’s **not strictly required** to remain a simple queue —  
it can be extended into a **double-ended queue (deque)** to enable additional functionality,  
such as rollback and error recovery in case of **malicious or unconfirmed blocks**.

In some implementations, a **“confirm-only PQP mode”** may be used —  
where a block’s `pqp_entry` is **not added** to the PQP until the block is **confirmed**.  
This ensures that only validated and trusted parents participate in the next round of block creation.


### 🔒 In a Confirmed-Only Consensus Variant

> Only blocks that are confirmed or finalized are allowed to enter the PQP, so this problem won't arise.

- 🚫 Prevents unconfirmed blocks from ever entering the **PQPool**.
- 🧱 Guarantees only stable, trusted blocks can become parents.
- ⚙️ Eliminates the need for rollback mechanisms entirely.


---



## ✅ TreeChain’s Structure Tolerates Partial Growth

- 🌳 **TreeChain is not strictly binary or trinary** — the 2 or 3-child model is just the ideal case.
- 🧩 **Parents are not required to have both children** at the same time.
- ❌ If a **child is invalidated**, the remaining child still stays valid and active.
- ✂️ Similar to **Merkle tree pruning** — we trim only the bad branch, not the whole structure.
- 🔁 The tree continues to grow from the valid parts without requiring full resets.


### ✨ Key Insight: Resilience of TreeChain

- ⚠️ Even when a block in **TreeChain** is found **malicious**...
- 🌳 ...the **tree doesn't collapse** like in traditional blockchains.
- 🔄 It **adapts and continues** from unaffected valid paths.
- 🚫 No need to discard the entire chain — only the affected subtree is pruned.
- ✅ This is the **core strength** of TreeChain over **linear blockchain models**.
---

### 🛡️ Is the Parent Queue Pool (PQP) Centralized?

- ❌ No — although the PQP may look like a global scheduler, it is **completely decentralized**.
- ✅ Each `pqp_entry` is embedded inside blocks and validated independently by every node.
- 🔁 All updates to the PQP are **deterministic**, **signed**, and **locally reconstructable**.
- 🌐 There is **no central authority**, no off-chain queue manager, and no extra protocol layer needed.
- 🧠 Think of PQP like a **Merkle Tree root** — all nodes agree on it, but no one "hosts" it.


