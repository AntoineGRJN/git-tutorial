## 1. Explain what is a relaxation of an optimization problem and how it can be used to help solving the problem.
**Definition**  
A relaxation of an optimization problem consists of transforming it into a simpler (typically continuous) problem by loosening some of the constraints. The aim is to obtain a problem that is easier to solve, for instance, a linear programming (LP) problem derived from a mixed-integer problem.

**Purpose**  
Relaxations are used to:
- Compute **dual bounds** for minimization problems (OD_BranchAndBound.pdf, p.4-6)
- Indicate potential **infeasibility** of the original problem if the relaxation is infeasible (OD_BranchAndBound.pdf, p.9)
- Guide solution algorithms like **branch-and-bound** by giving useful information for branching (OD_BranchAndBound.pdf, p.11)

**Types**  
- **Linear programming relaxation**: replace integrality constraints by continuous ones (OD_BranchAndBound.pdf, p.7)
- **Lagrangian relaxation**: dualize certain constraints to decompose the problem

**Illustration**  
In practice, solving the LP relaxation often gives insight into which constraints are critical and helps narrow down the search space for the full problem.

**Citation**: OD_BranchAndBound.pdf, pages 4–9

## 2. Explain what is the linear programming relaxation of a mixed-integer programming model and the implications it has for the branch-and-bound algorithm.
**Definition**  
The linear programming (LP) relaxation of a mixed-integer programming (MIP) model is obtained by relaxing the integer constraints and allowing variables to take continuous values within bounds.

**Formulation Example**  
MIP:  
minimize $c^T x + d^T y$  
subject to $Ax + Gy \leq b$, $x \in \mathbb{Z}_+^n$, $y \in \mathbb{R}_+^m$  
LP Relaxation:  
minimize $c^T x + d^T y$  
subject to $Ax + Gy \leq b$, $x \in \mathbb{R}_+^n$, $y \in \mathbb{R}_+^m$  
(OD_BranchAndBound.pdf, p.7)

**Implications for Branch-and-Bound**  
- The LP relaxation provides a **dual bound** (lower bound for minimization) (OD_BranchAndBound.pdf, p.4)
- If the LP solution is **integral**, it is also optimal for the MIP (OD_BranchAndBound.pdf, p.9)
- If not, the solution is used to **branch** on a fractional variable and continue recursively (OD_BranchAndBound.pdf, p.11)

**Usefulness**  
- Efficient LP solvers make it practical to compute bounds at each node of the branch-and-bound tree
- Quality of the LP relaxation heavily affects solver performance

**Citation**: OD_BranchAndBound.pdf, pages 7–11

## 3. Give a pseudocode description of the branch-and-bound algorithm and explain its main steps.
**Pseudocode**

```
function BranchAndBound(problem):
    best_solution ← None
    best_value ← ∞
    queue ← [problem]

    while queue not empty:
        node ← dequeue(queue)
        solution, bound ← solve_LP_relaxation(node)

        if bound ≥ best_value:
            continue  # prune by bound

        if solution is integer:
            if value(solution) < best_value:
                best_solution ← solution
                best_value ← value(solution)
        else:
            subproblems ← branch_on_fractional_variable(solution)
            enqueue(queue, subproblems)

    return best_solution
```

**Main Steps**  
- **Solve LP relaxation** to obtain a lower bound (OD_BranchAndBound.pdf, p.7)
- **Check integrality**: if solution is integer, compare with best known value
- **Branching**: split the problem based on fractional variable
- **Prune**: discard nodes where LP bound is worse than current best (OD_BranchAndBound.pdf, p.11–12)
- **Node selection**: strategies like DFS, BFS, best-bound (OD_BranchAndBound.pdf, p.14)

**Citation**: OD_BranchAndBound.pdf, pages 7–14

## 4. Explain the cases in which we can solve a discrete problem by solving one linear optimization problem and give examples of well-known problems of that class.
**Main Case**  
Discrete problems can be solved directly using their LP relaxation when the constraint matrix is **totally unimodular (TU)** and the right-hand side is integral.

**Why it Works**  
- A TU matrix ensures all vertices of the feasible region are integer (OD-TU-2023.pdf, p.3–4)
- Therefore, solving the LP returns an integer solution (OD-TU-2023.pdf, p.4)

**Examples**  
- Maximum flow problem  
- Shortest path problem  
- Minimum cost flow  
- Matching problem in bipartite graphs  
(OD-TU-2023.pdf, p.8)

**Citation**: OD-TU-2023.pdf, pages 3–4, 8

## 5. Explain what are totally unimodular matrices, how to recognize them and why it is important for discrete problems.
**Definition**  
A matrix is **totally unimodular (TU)** if every square submatrix has determinant 0, 1, or −1. This ensures integer solutions to LPs (OD-TU-2023.pdf, p.3).

**Recognition Criteria**  
- Determinant check for all square submatrices (general method)
- Sufficient conditions:
  - Entries in {−1, 0, 1}
  - At most two non-zero entries per column
  - Rows partitionable into two sets with alternating signs (OD-TU-2023.pdf, p.5–6)

**Importance**  
- LPs with TU matrices and integer right-hand side yield integer optimal solutions
- Removes need for integer constraints in some problems (OD-TU-2023.pdf, p.4, 7)

**Examples of TU Matrices**  
- Node-arc incidence matrix of directed graph
- Node-edge matrix of bipartite graph

**Citation**: OD-TU-2023.pdf, pages 3–7

## 6. Explain the principle of a local search heuristic and its use within a metaheuristic.
**Local Search Principle**  
- Begin with an initial feasible solution.
- Define a **neighborhood** of that solution.
- Iteratively move to a better solution in the neighborhood.
- Stop when a **local optimum** is reached (no better neighbor exists).
(OD_heuristics.pdf, p.4)

**Challenges**  
- May get **stuck in local minima**.
- Solution quality depends on neighborhood design.
(OD_heuristics.pdf, p.5)

**Use in Metaheuristics**  
Metaheuristics enhance local search to escape local optima:
- **Simulated Annealing**: accepts worse solutions with some probability (OD_heuristics.pdf, p.8–9)
- **Tabu Search**: avoids returning to recently visited solutions (OD_heuristics.pdf, p.10)
- **Feasibility Pump**, **Very Large Neighborhood Search** also use local search principles (OD_heuristics.pdf, p.6–7)

**Citation**: OD_heuristics.pdf, pages 4–10

## 7. Explain the concept of a pair of dual problems. Illustrate by using one example from the course.
**Duality Concept**  
In linear programming, every problem (the **primal**) has an associated **dual** problem with its own variables and constraints.

**Standard Forms**  
Primal (P):  
minimize $c^T x$, s.t. $Ax = b$, $x \geq 0$

Dual (D):  
maximize $b^T p$, s.t. $A^T p \leq c$, $p \in \mathbb{R}^m$
(LP_review.pdf, p.16)

**Duality Theorem**  
If $x^*$ and $p^*$ are optimal for the primal and dual, then:  
$c^T x^* = b^T p^*$  
Also, $c^T x \geq b^T p$ for any feasible pair.

**Example**  
Given a resource allocation LP, the dual may represent shadow prices on the resources.

**Citation**: LP_review.pdf, page 16

## 8. Explain what is the Steiner tree problem, compare different formulations of it and give an application of the problem.
**Problem Description**  
Given a graph with edge costs and a subset of terminal nodes $T$, the goal is to find a minimum-cost tree connecting all terminals. Additional nodes may be used.

**Formulations**  
1. **Cutset Formulation**: Require at least one edge between any subset $S \subset V$, $S \cap T ≠ ∅$, and $T \setminus S ≠ ∅$  
2. **Partition Formulation**: Enforce edge cuts over a partition of terminal subsets  
3. **Directed Flow Formulation**: Use flow from a root to all other terminals to encode connectivity  
(OD-formulations-2023.pdf, p.20–21)

**Comparison**  
- Directed and partition formulations are tighter relaxations than the simple cutset model.  
- Tight relaxation ⇒ better dual bounds.

**Application**  
- Network design: laying cables, pipelines, or circuit design where only some endpoints matter.

**Citation**: OD-formulations-2023.pdf, pages 18–21

## 9. Explain the Traveling Salesman Problem, compare different formulations of it and give a few applications of it.
**Problem Description**  
Find the shortest possible tour visiting each city exactly once and returning to the starting city. It is NP-hard.

**Formulations**  
1. **Subtour Elimination**:  
   - Each node has degree 2  
   - Prevent disconnected cycles: for any subset $S \subset V$, $\sum_{e∈E(S)} x_e ≤ |S|-1$
2. **Cutset Formulation**:  
   - Require at least two edges crossing any cut between a proper subset and its complement  
(OD-formulations-2023.pdf, p.15)

**Applications**  
- Route optimization  
- Logistics and delivery  
- Microchip design  
- Genome sequencing

**Citation**: OD-formulations-2023.pdf, page 15

## 10. Explain the facility location problem, compare two formulations of it and explain why one is better than the other.
**Problem Description**  
Choose which facilities to open and how to assign clients to them, minimizing fixed and assignment costs.

**Formulation 1: Classical**  
- Binary variable $y_i = 1$ if facility $i$ is open  
- $x_{ij}$ = 1 if client $j$ is assigned to facility $i$  
- Constraints: each client assigned to one facility; $x_{ij} ≤ y_i$

**Formulation 2: Aggregated**  
- Replace $x_{ij} ≤ y_i$ with $∑_j x_{ij} ≤ n y_i$  
- Weaker: allows fractional assignment in LP relaxation

**Better Formulation**  
- The classical model has a **tighter LP relaxation**, leading to better dual bounds and faster solving.
(OD-formulations-2023.pdf, p.10)

**Citation**: OD-formulations-2023.pdf, page 10

## 11. Explain how to express a piecewise linear function using mixed-integer linear programming.
**Goal**  
Model a function $f(x)$ defined by piecewise linear segments.

**Modeling Method**  
- Define $λ_i$: continuous weights for interpolation  
- Use binary variables $y_i$: indicate active segment  
- Ensure $∑ λ_i = 1$, $∑ y_i = 1$  
- Use constraints to activate only one piece:
  - $λ_1 ≤ y_1$, $λ_n ≤ y_{n−1}$
  - $λ_i ≤ y_{i−1} + y_i$, for $i = 2, ..., n−1$
(OD-formulations-2023.pdf, p.7)

**Citation**: OD-formulations-2023.pdf, page 7

## 12. Explain how to assess the strength of cutting planes.
**Definition of Strength**  
A cut is **strong** if it significantly reduces the feasible region without cutting off integer solutions.

**Geometric View**  
- A cut defines a **face** of the polyhedron.
- The **strongest** cuts define **facets**: maximal faces (OD_cutting_planes2023.pdf, p.10)

**Dominance**  
- Cut A dominates B if A's coefficients and RHS are tighter.
- Dominated cuts are weaker.

**Citation**: OD_cutting_planes2023.pdf, pages 9–10

## 13. Explain two generic ways to generate cutting planes for a mixed-integer program.
**1. Chvátal-Gomory Cuts**  
- Combine constraints with integer multipliers  
- Round RHS and coefficients down  
- Valid for integer hull  
(OD_cutting_planes2023.pdf, p.3)

**2. Mixed Integer Rounding (MIR) Cuts**  
- Apply rounding procedure using fractional parts  
- Useful when LP relaxation is fractional  
(OD_cutting_planes2023.pdf, p.6)

**Citation**: OD_cutting_planes2023.pdf, pages 3, 6

## 14. Explain why having an exponential number of subtour elimination constraints in the formulation of the traveling salesman problem does not prevent us from computing the linear programming relaxation quickly.
**Reason**  
- Constraints are not all included initially.
- Use **lazy constraints** or **separation routines** to add only violated ones (OD_cutting_planes2023.pdf, p.15)

**User Cut Callback**  
- Add constraints only when violated in the current LP solution.
- Allows solving LP efficiently despite the theoretical size.

**Citation**: OD_cutting_planes2023.pdf, page 15

## 15. Explain what is the separation problem. Compare with the concept of lazy constraints.
**Separation Problem**  
Given a convex set $X$ and point $x 
otin X$, find a hyperplane (valid inequality) separating $x$ from $X$.  
Used to dynamically add violated constraints.

**Lazy Constraints**  
- Constraints too numerous to add from start  
- Added only when violated by an integer solution  
- Checked only at integer nodes

**Comparison**  
- Both reduce model size  
- Lazy constraints apply only at integer solutions, separation is broader

**Citation**: OD_cutting_planes2023.pdf, pages 16, 18

## 16. Explain the max flow and the min cut problems and give applications of the two problems. Explain a generic way to solve the max flow and the min cut.
**Max Flow Problem**  
Send as much flow as possible from a source $s$ to sink $t$ through a network.  
Constraints: flow conservation, capacity limits (OD_flow_2025.pdf, p.2–3)

**Min Cut Problem**  
Partition nodes into $S, T$ with $s ∈ S$, $t ∈ T$; minimize capacity of edges from $S$ to $T$ (OD_flow_2025.pdf, p.4)

**Max-Flow Min-Cut Theorem**  
Maximum flow = minimum cut capacity (OD_flow_2025.pdf, p.14)

**Generic Solution**  
- Use **augmenting paths** in residual graph  
- Ford-Fulkerson algorithm (OD_flow_2025.pdf, p.11)

**Applications**  
- Image segmentation (OD_flow_2025.pdf, p.8)  
- Network routing  
- Bipartite matching (OD_flow_2025.pdf, p.16)

**Citation**: OD_flow_2025.pdf, pages 2–4, 8, 11, 14, 16
