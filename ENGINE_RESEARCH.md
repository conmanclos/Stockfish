# Strength Research Lab

This branch is an isolated research track based on the upstream Stockfish baseline.

## Baseline

- Repository: `conmanclos/Stockfish`
- Baseline branch: `master`
- Baseline commit: `5d03b6e578a5eedc822e4563bdbb89e762fc2a59`
- Baseline status: matches the corresponding upstream Stockfish commit at the time this lab was created.
- Default evaluation network: `nn-bddd9d57eb87.nnue`

Do not describe this branch as stronger than Stockfish until a controlled match demonstrates that result.

## Objective

Develop a reproducibly stronger engine while preserving a known-good baseline. The target is a statistically supported Elo gain against the exact baseline at specified hardware, thread count, hash size, and time control. A separate claim of beating current upstream Stockfish requires a second comparison against a pinned upstream revision and its matching network.

## Non-negotiable rules

1. Keep the baseline intact. All experiments belong on this branch or a child branch.
2. One conceptual change per experiment. Do not combine search, evaluation, network, and compiler changes in one result.
3. Never infer playing strength from node count, benchmark speed, tactical-suite score, or a single game.
4. Record the exact source commit, network SHA-256, compiler, build flags, CPU, OS, thread count, hash size, tablebase configuration, opening set, adjudication rules, and time control.
5. Reject changes that introduce correctness, reproducibility, memory-safety, or protocol regressions, even if a short match appears positive.
6. Keep performance-only changes separate from playing-strength changes.
7. Preserve GPLv3 notices and source obligations for distributed derivatives.

## Validation ladder

### Gate 0 — Reproducible baseline

- Build the pinned baseline and save its engine binary hash.
- Verify the NNUE file against its filename-derived SHA-256.
- Record the built-in benchmark signature.
- Run standard perft positions, including Chess960.
- Run reproducible-search checks at fixed node limits.
- Capture UCI protocol behavior for stop, ponderhit, position, go nodes, go depth, MultiPV, and ucinewgame.

### Gate 1 — Correctness and safety

- Run perft and targeted move-generation tests after every move-generation or position-state change.
- Exercise castling, Chess960 castling, en passant, promotions, repetition, the fifty-move rule, null moves, and check evasions.
- Run address/undefined-behavior and thread sanitizers on dedicated builds where supported.
- Review TT and shared-history concurrency assumptions; do not “fix” intentional racy TT behavior without measuring both correctness and Elo consequences.
- Validate NNUE loading, format/hash compatibility, accumulator refresh, and incremental updates.

### Gate 2 — Strength experiments

- Use paired games with colors reversed and a fixed opening suite.
- Keep both engines on identical hardware and resource limits.
- Use a sequential probability ratio test (SPRT) with predeclared Elo bounds and error rates.
- Report Elo, confidence/LLR, W/L/D, games, time control, and benchmark speed.
- Repeat promising results at a longer time control before accepting changes that affect search behavior.
- Test against both the pinned baseline and pinned current upstream.

### Gate 3 — Generalization

- Repeat on a second CPU architecture and at least one different thread count.
- Check low-hash and high-hash behavior.
- Test short and long time controls.
- Confirm the gain is not caused by opening bias, color imbalance, hardware asymmetry, or a benchmark artifact.

## Research priorities

1. **Search:** investigate pruning/reduction calibration, move ordering, time management, singular extensions, and search instability. Every change needs paired self-play; tuning values by intuition is not acceptance evidence.
2. **Evaluation:** investigate feature representation and NNUE architecture/training targets. New networks must be compatible with the exact feature-transformer format and must be evaluated with controlled self-play, not just lower training loss.
3. **Training data:** compare search-tree labels, self-play distributions, tactical/endgame coverage, and data deduplication. Record training provenance and network hashes.
4. **Performance:** profile real search workloads before changing SIMD, accumulator updates, memory layout, NUMA placement, or transposition-table behavior. A faster benchmark is useful only if it converts into more useful search or better Elo.
5. **Endgames and correctness:** use perft, tablebase positions, tactical suites, and adversarial FENs to detect regressions that self-play can miss.

## Experiment record template

For each experiment, record:

- Hypothesis:
- Exact source diff:
- Baseline commit and candidate commit:
- Network filename and SHA-256:
- Build/compiler/flags:
- CPU/OS:
- Threads/hash/tablebases:
- Opening suite and time control:
- SPRT bounds and error rates:
- Games, W/L/D, Elo, confidence/LLR:
- Bench signature and nodes/second:
- Correctness/sanitizer results:
- Decision: accept / reject / inconclusive

## Current audit notes

- The starting repository is an upstream Stockfish mirror, not a pre-existing independent engine.
- The pinned baseline includes the current default NNUE network named above.
- The repository already contains perft, reproducible-search, signature, and broader test tooling.
- No confirmed correctness defect has been established by the initial source review. Potential risks in search, move generation, NNUE, and concurrency must be demonstrated with a reproducer before being called bugs.
- Functional strength changes require statistically meaningful self-play. The upstream contribution guide explicitly expects Fishtest evidence for functional changes.

## Current status

This document establishes the research protocol only. It does not claim an Elo gain, a completed audit, or a stronger engine. The first engine modification should be a narrowly scoped, profile- or match-supported experiment with a recorded rollback point.
