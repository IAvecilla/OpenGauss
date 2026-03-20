# /lean4:optimize — Interactive AMO-Lean Optimization Workflow

You are running an **interactive AMO-Lean optimization workflow**. Your goal is to
transform a Lean 4 mathematical specification into optimized, formally verified
C or Rust code using equality saturation.

## Prerequisites

- AMO-Lean source is available at `$AMO_LEAN_ROOT`
- OptiSat (verified e-graph engine) is available at `$OPTISAT_ROOT`
- The Lean LSP MCP server is available for type-checking and goal inspection

## Workflow Steps

### 1. Understand the specification

- Read the user's Lean 4 specification or accept their description of the mathematical operation
- If the spec doesn't exist yet, help the user write it as a Lean 4 definition
- Verify the spec type-checks using `lake build` or the Lean LSP MCP

### 2. Set up AMO-Lean

- Ensure the AMO-Lean project is configured to work with the user's Lean project
- Check that `lakefile.lean` includes the necessary AMO-Lean and OptiSat dependencies
- Run `lake build` to verify the setup compiles

### 3. Define rewrite rules

- Identify algebraic identities from Mathlib that apply to the specification domain
- Write rewrite rules as Lean 4 theorems that the equality saturation engine can use
- Each rule must have a corresponding Lean proof — no axioms or `sorry`
- Use the OptiSat e-graph API to register the rewrite rules

### 4. Run equality saturation

- Feed the specification and rewrite rules into AMO-Lean's equality saturation pipeline
- The e-graph explores equivalent representations of the computation
- A cost model selects the optimal representation (minimizing operations, enabling SIMD, etc.)

### 5. Generate verified output

- AMO-Lean emits optimized C or Rust code from the best e-graph extraction
- Each transformation step has a corresponding Lean proof of semantic equivalence
- Verify the full proof chain compiles: `lake build`

### 6. Review with the user

- Present the optimized code and explain the key transformations applied
- Show the proof structure: which algebraic identities were used
- Discuss any trade-offs (code size vs. speed, SIMD width, target architecture)

## Key AMO-Lean Concepts

- **E-graph**: A data structure that compactly represents many equivalent programs
- **Equality saturation**: Exhaustively applying rewrite rules to discover all equivalent forms
- **Cost model**: Selects the best program from the e-graph based on performance criteria
- **Traceability**: Every optimization step maps back to a Lean-verified algebraic identity

## Tips

- Start with the simplest version of the spec and optimize incrementally
- Use `lake build` frequently to catch proof issues early
- If a rewrite rule is hard to prove, check Mathlib for existing lemmas
- The Lean LSP MCP server is your best tool for exploring types and goals
