# 🌳 TreeChainTheory – Rethinking the Blockchain Structure

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




## 📐 Tree Structure Explained

TreeChainTheory introduces a structural shift in how blocks are linked — moving from a single linear chain to a multi-branching tree.

Unlike traditional blockchains where each block has exactly one child (forming a straight line), TreeChainTheory allows each block to have **multiple children**, opening up new possibilities for parallel processing and scalability.

### 🌳 Ideal Case: 2 Children Per Parent

To maintain simplicity and clarity, our theory assumes an **ideal case** where each parent block produces exactly **2 children**. This forms a balanced tree-like structure that is easy to model, visualize, and simulate.


         Genesis
          /   \
       B1      B2
      / \     /  \
    B3   B4  B5   B6
    ...


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

### 🧬 Key Design Principles

- **Decentralization**: Multiple leaders operate simultaneously to create children blocks.
- **Parallelism**: Multiple children can be created in parallel, increasing throughput.
- **Determinism**: The process for choosing the next parent and assigning leaders is deterministic via a queue system (**Parent Queue Pool**).
- **Flexibility**: Though we assume 2 or 3 children per parent here, the model supports N-ary trees.

### 📦 The Parent Queue Pool

A central part of the system is the **Parent Queue Pool**, which tracks all eligible parent blocks. Here's how it works:

- When a block is created, it is added to the parent queue.
- Once it receives N children, it is removed from the queue.
- The queue ensures fair rotation and avoids centralized bottlenecks.

> This simple but powerful idea sets the stage for scalable and decentralized block creation — replacing linear limits with branching potential.

📘 **Further details:** See [`parent-queue-pool.md`](./parent-queue-pool.md)  
for the complete explanation of how the **Parent Queue Pool (PQP)** manages parent selection and rotation.

    
