# /lean4:autooptimize — Autonomous AMO-Lean Optimization Workflow

You are running an **autonomous AMO-Lean optimization workflow**. Your goal is to
take a Lean 4 mathematical specification and produce optimized, formally verified
C or Rust code using equality saturation — end-to-end, without interactive guidance.

## Prerequisites

- AMO-Lean source is available at `$AMO_LEAN_ROOT`
- OptiSat (verified e-graph engine) is available at `$OPTISAT_ROOT`
- The Lean LSP MCP server is available for type-checking and goal inspection

## Autonomous Workflow

### Phase 1: Specification Analysis

1. Read the user's specification or forwarded arguments
2. If a natural language description is provided, formalize it as a Lean 4 definition
3. Verify the spec type-checks via `lake build`
4. Identify the algebraic domain (linear algebra, polynomials, number theory, signal processing, etc.)

### Phase 2: Environment Setup

1. Configure the Lean project to depend on AMO-Lean and OptiSat
2. Add the necessary imports to the project's lakefile
3. Run `lake build` to ensure the full dependency chain resolves

### Phase 3: Rewrite Rule Discovery

1. Search Mathlib for algebraic identities relevant to the specification domain
2. Write each identity as a Lean 4 rewrite rule with a formal proof
3. Register rules with the OptiSat e-graph engine
4. Iterate: if a proof fails, try alternative lemma paths or simplify the rule
5. Target at least the core identities needed for meaningful optimization

### Phase 4: Equality Saturation & Extraction

1. Feed the spec + rules into AMO-Lean's equality saturation pipeline
2. Configure the cost model for the target (minimize ops, maximize SIMD utilization, etc.)
3. Run saturation until fixpoint or iteration limit
4. Extract the optimal program from the e-graph

### Phase 5: Code Generation & Verification

1. Emit the optimized C or Rust code from the extracted program
2. Verify that every transformation has a corresponding Lean proof
3. Run `lake build` to confirm the full proof chain compiles without `sorry`
4. If any proofs fail, fix them before proceeding

### Phase 6: Output

1. Write the generated code to the project directory
2. Summarize:
   - What algebraic identities were applied
   - Estimated speedup or optimization characteristics
   - Any limitations or assumptions in the generated code
3. If the optimization could not proceed, explain the blockers clearly

## Recovery Strategy

- If `lake build` fails at any stage, read the error, fix the issue, and retry
- If a rewrite rule proof is stuck, try:
  - Searching Mathlib with `exact?`, `apply?`, or `search_proof_tactic`
  - Decomposing the rule into smaller lemmas
  - Using the Lean LSP MCP for goal inspection
- If equality saturation times out, reduce the rule set or increase the iteration limit
- If the specification is too complex, decompose it into sub-specifications

## Session Contract

- Work autonomously — do not ask the user for guidance unless truly stuck
- Keep all changes inside the current Lean project
- Every optimization must be backed by a Lean proof — no `sorry`, no `axiom`
- Explain blockers clearly if the optimization cannot proceed
