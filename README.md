# Evolutionary Algorithm for TSP/ATSP
This project tackles the **Travelling Salesperson Problem** (TSP) and its **asymmetric** variant (ATSP) using an **Evolutionary Algorithm** (EA) with **2-opt local search**.  
The method is designed to work on **all provided instances**:

- `g_*.npy`: **symmetric** (metric) TSP
- `r1_*.npy`, `r2_*.npy`: **asymmetric** ATSP (self-loops may be present)

The EA always builds **closed tours** (last → first edge included in the fitness).

---

## Design choices

### Initialization (population)
- **Greedy NN (Nearest-Neighbor)** on **symmetric** problems.
- **Greedy anti-isolation** on **asymmetric** problems: avoid leaving “orphan” nodes near the end by picking nodes whose *nearest neighbor* is the current last node, maximizing the margin vs. the *second nearest*.  
- **Random** individuals for diversity. Initial mix ≈ **60% greedy / 40% random**.

### Crossover
- **ERX – Edge Recombination** for metric TSP: preserves adjacency (edge set), very effective on symmetric instances.
- **OX – Order Crossover** for ATSP: preserves the relative order of genes, avoiding directional conflicts.

### Mutation
- **Inversion** (mini 2-opt) for symmetric problems.
- **Insertion** (remove + reinsert gene) for asymmetric problems, to preserve the overall order.

### Local Search
- **2-opt first-improvement** with a **move cap per child** (default ≈ `n/5`).  
  Benefit: large quality jump at limited cost.

### Selection / Replacement
- **Tournament selection** (k = 4).
- **Steady-state** with **implicit elitism**: children only replace the worst individuals if they’re better.

### Time budget
- Each instance gets a **time limit** (adaptive to `n`, between 5 s and 60 s).  
  This scales well without stalling on large instances. The limit is configurable.

---

## Main parameters

- `pop_size`: adaptive default (`max(40, min(120, n // 5))`)
- `offspring_per_iter`: adaptive default (`max(20, min(80, n // 4))`)
- `p_mut`: 0.30 for symmetric, 0.40 for asymmetric
- `two_opt_moves`: ≈ `n/5` per child
- `tournament_k`: 4
- `time_limit_s`: adaptive by default; can be overridden.


---

Made with  Simone Ventura, github: https://github.com/gambatz
