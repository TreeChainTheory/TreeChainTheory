# 🌳 TreeChainTheory – Reimagining the Blockchain Structure

> **What if a blockchain wasn’t a chain?**  
> Instead of a linear reverse-linked list, imagine a tree — branching, parallel, and scalable.  

**TreeChainTheory** explores a new data structure for blockchains, where blocks grow in a tree-like formation instead of a single sequential chain. This model introduces novel possibilities in speed, decentralization, and consensus design — aiming to overcome the limitations of traditional blockchain architecture.

## 🔍 Core Ideas
- 🌱 Replaces linear blockchains with a **tree** structure of blocks.
- ⚡ Aims to increase **scalability** & **throughput** by parallelizing block creation.
- 👥 Enables better **decentralization** with multiple active block leaders.
- 🔄 Introduces a **parent queue pool** for dynamic parent selection.
- 🚀 Designed to become the base for the **world's most scalable blockchain**.


## 🌲 Why Tree Over Chain?

Traditional blockchains use a linear, reverse-linked list structure where each block points to a single parent. While this keeps the system simple, it creates bottlenecks:

- ❗ Only one leader at a time → low parallelism
- 🔄 Sequential block production → slower finality
- 🧱 Each block depends on the last → centralization risks in leader selection

**TreeChainTheory** proposes a paradigm shift:

- 🧬 Each parent can give rise to multiple children (2 or 3).
- 🌐 Multiple leaders can produce blocks **in parallel**.
- 🔁 Parent queue pool dynamically schedules parents for next block production.

This makes the system more scalable, and fair — paving the way for a high-throughput , highly scalable decentralized network.


### 🔗 Block Relationships

- **Each block** has one parent (except the Genesis block).  
- **Each parent** can produce **Multiple children (N)**, handled by **(N) leaders** (1-aligned, 2-aligned..., N-aligned miners/validators/leaders).  
- **Alignment** defines the position of a block in the tree —  
  - A **1-aligned block** is created by a 1-aligned miner/validater/leader (first child).  
  - A **2-aligned block** is created by a 2-aligned miner/validater/leader (second child).
  - A **N-aligned block** is created by a N-aligned miner/validater/leader (N'th child). 
- A parent is **removed from the parent queue pool** once both of its aligned children are created.  
- The structure grows **horizontally and vertically**, forming a **tree** rather than a simple linear chain.


### 🧩 Block Composition & PQP Commitments

Each block contains more than just the usual `hash` and `parent_hash` — it also carries:
- `pqp_commitment` → the **SHA-256 hash** of multiple internal fields, including the block’s own `hash`.
- so yes the `pqp_commitment` is computed after the hash is computed.
- `prev_pqp_commitment` → a reference to the **previous block of the same alignment** in the tree.

This dual-reference design makes every block **cryptographically linked in two directions**:
1. **Vertical linkage** — through `parent_hash`, pointing to its parent’s `hash`.  
2. **Horizontal linkage** — through `prev_pqp_commitment`, connecting to the previous same aligned block.(this is not strictly horizontal linkage)


### 🕸️ DAG-Like Yet Distinct

- Because each block references both its **parent** and its **previous same aligned block**, it effectively has **two parent references** — resembling a **Directed Acyclic Graph (DAG)**.  
- However, TreeChainTheory is **not a DAG in practice**:  
  - The **alignment rules** and **parent queue structure** ensure strict determinism.  
  - No arbitrary cross-links are allowed — each block’s secondary linkage is always within its alignment path.  
  - This makes TreeChainTheory **structured like a tree, secured like a chain, and extended like a DAG** — a unique hybrid design enabling both parallelism and order.

---


# 🌳 TreeChain & PQP 

The **TreeChain** framework reimagines blockchain architecture by replacing the traditional *linear chain* with a **tree-structured block system** — designed to support **parallel consensus**, **deterministic validation**, and **scalable finality**.

- TreeChainTheory introduces a structural shift in how blocks are linked — moving from a single linear chain to a multi-branching tree.

- Unlike traditional blockchains where each block has exactly one child (forming a straight line), TreeChainTheory allows each block to have **multiple children**, opening up new possibilities for parallel processing and scalability.

## 🌴 TreeChain Overview

- **TreeChain** represents the entire blockchain as a **logical tree**, where each parent block can produce multiple aligned child blocks simultaneously.  
- Unlike recursive trees, TreeChain uses **index-based mappings** (`IndexMap`) for both blocks and their child relationships, achieving **O(1)** access time for lookups, insertions, and traversals.  
- This structure is **consensus-agnostic** — it seamlessly adapts to **PoW**, **PoS**, or **PoH** models by integrating relevant block creation and validation logic.  
- Each block maintains dual continuity:
  - **Vertical linkage** through `parent_hash` (tree structure)
  - **Horizontal linkage** through `prev_pqp_commitment` (aligned continuity)
- This ensures every new block is cryptographically bound to both its *parent* and *aligned sibling chain*, maintaining strong consistency across concurrent branches.

### 🌿 Key Characteristics
- **Parallel block growth** — multiple branches expand in real time.   
- **Efficient traversal** — constant-time lookups via index mapping.  
- **Placeholder handling** — missing or stale blocks are represented by placeholders to preserve structural integrity.  
- **Consensus flexibility** — TreeChain remains compatible with hybrid or dynamic consensus models.

## 🧩 Parent Queue Pool (PQP) 👓 Overview 

The **Parent Queue Pool (PQP)** is the **ordering and scheduling layer** of TreeChain —  
responsible for managing **which parent block** is currently active and **in what order** new parents are processed.


Since TreeChain is **not linear** and multiple parent branches can exist simultaneously,  
PQP enforces a **global parent order** so that **only one parent** is considered “active” for new child block creation at any given moment.


Each new block generates a **ParentQueueEntry**, storing:
- `queue_index` → Position in PQP sequence.  
- `align` → Alignment level (1-aligned, 2-aligned, etc.).  
- `block_hash` & `parent_hash` → Linkage identifiers.  
- `pqp_commitment` & `prev_pqp_commitment` → Commitment continuity proofs.  
- `leader_address` & `signature` → Validator or miner authentication.  
- `timestamp` → Time or slot marker (for PoH).  (optional for remainig models)


### 🌳 Ideal Case: 2 Children Per Parent

- To maintain simplicity and clarity, our theory assumes an **ideal case** where each parent block produces exactly **2 children**. This forms a balanced tree-like structure that is easy to model, visualize, and simulate.


           Genesis
            /   \
         B1      B2
        / \     /  \
      B3   B4  B5   B6
    

- **Tree** generalizes the idea of Treechain across all the major **Proof-of-Work**, **Proof-of-Stake (PoS)** and **Proof-of-History (PoH)** models — maintaining a consistent data structure while adapting the consensus logic.  
- Instead of growing a single linear chain of confirmed blocks, it grows **multiple branches concurrently**, allowing **parallel leader validation** and **time-synchronized verification** (for PoH).  
- Structurally, it’s still **tree-based but index-driven**, not recursive — meaning it relies on an **index map** to track parent-child and aligned relationships efficiently.
- The **index map** provides **O(1)** access by `queue_index` or `block_hash`, ensuring scalable & optimized lookups even as the network grows.
- The **Parent Queue Pool (PQP)** ensures every block attaches to:
  - A valid **parent block** (`parent_hash`)
  - A valid **previous aligned block** (`prev_pqp_commitment`)
  - A **stake/time-validated leader or Miner (PoW model)**
- When an expected block is missed (due to inactivity or disqualification), a **placeholder block** is inserted to preserve the deterministic structure — ensuring indexing consistency across all nodes.
- This design allows **time-synchronized parallel validation** (PoH) or **stake-weighted participation** (PoS) — both with deterministic structural ordering.

## 🌴 Tree Structure

```rust
pub struct TreeChain {
    pub blocks: IndexMap<String, Block>,
    pub children_map: IndexMap<String, Vec<String>>,
    pub count: usize,
}
```

- **TreeChain** represents the complete tree view of the blockchain under **PoS/PoH consensus**.  
  It extends the traditional structure with **validator/time tracking mechanisms**.

- **`blocks` → `IndexMap<String, Block>`**
  - Stores every block (**confirmed**, **pending**, or **placeholder**) using its **hash** as the key.  
  - Preserves insertion order, ensuring **deterministic traversal** based on `queue_index` or **timestamp (PoH)**.  
  - Enables **O(1)** block lookup and **predictable ordering** across validators.

- **`children_map` → `IndexMap<String, Vec<String>>`**
  - Maps each **parent block’s hash → list of its children block hashes**.  
  - Maintains the **tree structure** without recursion, supporting **parallel verification paths**.  
  - Allows **rapid identification** of branches and **aligned leader outputs**.

- **`validator_set` → `IndexMap<String, ValidatorInfo>` (only for Pos/Poh models)**
  - Tracks **validators** currently participating in block creation.  
  - Each validator entry includes:  
    - 💠 **Stake or weight** (for PoS)  
    - ⏱️ **Slot/timestamp alignment** (for PoH)  
    - 📊 **Performance / participation metrics**

- **`count` → `usize`**
  - Counts only **confirmed and valid** blocks.  
  - **Placeholder** or **orphaned** blocks are retained structurally but excluded from active count.

## ⚙️ Workflow
---

TreeChain coordinates **validator alignment**, **time progression**, and **parent scheduling** across all branches.

### 🧩 1. Aligned Block Creation

Each parent may produce up to **N children**, one for each **aligned validator or slot**:

- 1-aligned validator/block  
- 2-aligned validator/block  
- …  
- N-aligned validator/block  

Each aligned block must:

- Reference the **parent’s hash** (for vertical linkage).  
- Reference the **previous same aligned block’s `PQP` commitment** (for continuity).  
- Including **PoS or PoH models**


### 🧱 2. Block Linking & PQP Commitments

Each block includes:

- **`pqp_commitment`** → hash commitment covering internal fields (including block hash, stake/timestamp, and alignment info).  
- **`prev_pqp_commitment`** → points to the previous block of the same alignment.

These dual links establish:

- **Vertical linkage** — via `parent_hash`  
- **Horizontal/time linkage** — via `prev_pqp_commitment`

Together, these ensure **structural consistency**, **temporal ordering**, and **tamper resistance** across aligned validator paths.

### 🔁 3. Dynamic Parent Switching

Once an aligned validator completes its block for the current parent:

- It immediately starts preparing for the **next eligible parent** as per PQP rotation.  
- Validators automatically switch to the next parent once **any aligned child of that next parent** is broadcast.

This maintains continuous consensus flow, ensuring:

- No idle waiting between parents.  
- No duplicated mining or staking efforts.  
- Fair competition among all alignment groups.

---

### 🧩 4. Confirm-Only PQP Mode

To improve finality and avoid forks:

- Blocks are **only added to the PQP after confirmation** (based on stake majority or PoH slot verification).  
- Unconfirmed or invalid blocks are **rejected or rolled back**, but **placeholders** ensure the index map remains stable.

This guarantees all nodes maintain an **identical PQP index view**, even in rollback or partial confirmation scenarios.


### 🌐 Outcome: Unified Parallel Consensus Layer

Under **PoW/PoS/PoH**, TreeChain evolves into a next-generation consensus model that is:

- ⚡ **Parallel** — Multiple validators or time slots produce blocks simultaneously, maximizing throughput.  
- 🧭 **Deterministic** — Time or stake sequencing ensures predictable and fair leader rotation.  
- 🔒 **Secure** — Dual-link commitments (parent + PQP) guarantee tamper resistance across all validator paths.  
- 🧩 **Efficient** — Maintains **O(1)** lookups and updates, even across large validator networks.

Together, these properties make **TreeChain** a **unified, parallel, and verifiable consensus layer**, seamlessly merging **time**, **stake**, and **structure** into one scalable blockchain framework.



### 🧬 Key Design Principles

- **Decentralization**: Multiple leaders operate simultaneously to create children blocks.
- **Parallelism**: Multiple children can be created in parallel, increasing throughput.
- **Determinism**: The process for choosing the next parent and assigning leaders is deterministic via a queue system (**Parent Queue Pool**).
- **Flexibility**: Though we assume 2 or 3 children per parent here, the model supports N-ary trees.

# 📦 The Parent Queue Pool

A central part of the system is the **Parent Queue Pool**, which tracks all eligible parent blocks. Here's how it works:

- When a block is created, it is added to the parent queue.
- Once it receives N children, it is removed from the queue.
- The queue ensures fair rotation and avoids centralized bottlenecks.

> This simple but powerful idea sets the stage for scalable and decentralized block creation — replacing linear limits with branching potential.

📘 **Further details:** See [`parent-queue-pool.md`](./parent-queue-pool.md)  
for the complete explanation of how the **Parent Queue Pool (PQP)** manages parent selection and rotation.


---

## ⚙️ Transaction Alignment in TreeChainTheory
> **Please read this section after reviewing [`parent-queue-pool.md`](./parent-queue-pool.md)**  
> This section explains how transactions are aligned across different validator or miner groups to maintain order and prevent duplication.

- We perform Alignment operation on every transaction. so that its decided that which aligned miner/leader should pick that transaction.

### 🎯 Purpose of the Alignment Operation

In TreeChainTheory, every transaction undergoes an **alignment operation** before inclusion in a block.  
This ensures that the same transaction is **not included in multiple child blocks** — a critical step for preventing replay or duplication across parallel branches.

The alignment mechanism divides transactions among aligned leaders (miners/validators) based on **deterministic rules**, ensuring that each transaction belongs to **exactly one alignment group**.

### 🧩 Why Alignment Matters
- 🚫 Prevents **transaction repetition** across parallel branches.  
- ⚖️ Ensures **balanced distribution** of transactions among aligned miners or validators.  
- 🔒 Maintains **deterministic transaction placement**, critical for consensus consistency.  
- 🧱 Prevents exploitation where a single sender could trigger multiple simultaneous spends across branches.

## 🌐 TreeChainTheory Transaction Alignment Logic

### 💸 Normal Token Transfer
- Uses the **sender’s public key (`pubkey`)**.  
- Performs:
  `alignment_index = (last_digit_of(sender's_pubkey) % N) + 1`
> `N` represents "N" in N-ary Tree.
- The resulting `alignment_index` determines which aligned miner/validator handles the transaction.  
- This ensures that **all transactions from the same sender** always go to the **same alignment group** — preventing replay attempts.

### ⚙️ Smart Contract Creation
- Follows the **same logic** as normal transfers.  
- Uses the **creator’s public key (`pubkey`)** to determine alignment:
  `alignment_index = (last_digit_of(sender's_pubkey) % N) + 1`
- Guarantees deterministic assignment for all contract creation events, ensuring that parallel miners cannot create duplicate deployments.

### 📜 Smart Contract Interaction

- Implements a **two-step interaction model**:
  - **Register Tokens to SC** (sender allocates balance to the contract)
  - **Interact with SC** using only the registered balance

- **Registration Transaction Alignment:**
  - Uses the **sender’s public key (`pubkey`)**
  - `alignment_index = (last_digit_of(sender's_pubkey) % N) + 1`

- **Contract Call Alignment:**
  - Uses the **contract address** (not the sender’s key)
  - `alignment_index = (last_digit_of(contract_address) % N) + 1`

- **Why this model?**
  - If aligned by sender pubkey alone, different users could hit the same contract from **different lanes**, causing **state divergence**
  - Aligning by **contract address** forces all interactions of the same SC into a **single lane**, ensuring consistent state transitions

- **Why register tokens first?**
  - Without registration, a sender could **double spend** by:
    - Sending a normal transfer on one lane
    - Using the same tokens in a smart contract call on another lane
  - Registration restricts spending to the **SC-allocated balance**, eliminating cross-lane double spends

- **Outcome:**
  - Prevents **smart contract state divergence**
  - Ensures **deterministic SC execution ordering**
  - Blocks **cross-lane double spend attempts** by design


### 🧠 Why Not Use `txid` for Alignment?

If alignment were determined by the **transaction ID (`txid`)**,  
a malicious sender could generate **multiple valid transactions** using different inputs, producing separate `txid`s that map to **different alignment groups**.  
Each aligned miner might then include one of those transactions, all appearing valid in isolation — leading to **parallel double-spends**.

By instead aligning using the **sender’s public key**, TreeChainTheory guarantees that:
- All of a sender’s transactions are handled by **a single aligned miner or validator**.
- Cross-branch double-spend attempts are structurally impossible.
- Consensus remains **fair, deterministic, and cryptographically secure**.

> The alignment mechanism is what allows **TreeChainTheory** to maintain *parallelism without chaos* — enabling multiple aligned miners to operate simultaneously while guaranteeing that no transaction ever appears twice.

# 🌴 ₿ Explore the Latest TreeChainTheory Bitcoin Model
  - Implements the TreeChain architecture using **Proof-of-Work (PoW)** as the consensus mechanism.  
  - Demonstrates how **tree-based parallel block mining** can be achieved using traditional mining logic.  
  - Uses the **Parent Queue Pool (PQP)** to order parent blocks and ensure consistent parent-child scheduling.  
  - Serves as the **base implementation** for exploring advanced models like PoS and PoH.  
  - 🔗 **Explore the full repository here:**  
    👉 **[TreeChainTheory — Bitcoin Model (GitHub)](https://github.com/TreeChainTheory/TreeChainTheory---BitcoinModel)**

 > Make sure you read all the **TreeChainTheory** before exploring the **Bitcoint Model**
