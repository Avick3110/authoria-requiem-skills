# INDEX — Requiem skills rebuild

*Author: Aaron (+ Claude session) · 2026-10-30*

The router for this project. It follows the AI-native SDLC method (intent → recon → spec → plan →
build), opted into for a personal project. Aaron agrees each stage before the next starts.

## Status

**Stage: Recon (pass 2), 2026-10-31.** Aaron agreed `intent.md` on 2026-10-30. Recon pass 1 (the
control eval and its re-mining) is done. In pass 2, the census of ARR's handmade patches and all
of its split hunts are done (see `recon.md`'s findings index), and so is recon A1–A5. Heisen answered
the questions doc on 2026-10-31. His reference stance (Requiem and its addons first) and NPC doctrine are
in `decisions.md`, and each split's fate is in `recon/splits/README.md`. Aaron settled D5, C1, C2, the
trigger-eval setup and Q29 the same day (`decisions.md`): the new skills are graded against the spec,
not against 1.x. Codex support and an installable release were added as targets A8 / A9
(desk pass done; live pass open). Still open: A6, A8 / A9 live, the spec-session topics in `carried-forward.md`, and the targets that
need Aaron's own patching sessions. The latest handoff in `../handoffs/` says where to pick up.

## Where things live

| Path | Holds |
|---|---|
| `intent.md` | Stage 1: problem, outcome, affected users and systems, constraints, out of scope, open questions |
| `recon.md` | Stage 2: the pass 1 reading order (what is known and where), and the proposed pass 2 targets |
| `spec.md` | Stage 3: numbered requirements, design, constraint table, areas of concern, end-to-end check (created when the stage starts) |
| `plan.md` | Stage 4: order of work citing requirements, risks, proof, rollback (created when the stage starts) |
| `decisions.md` | Every gate and decision, dated, append-only |
| `eval-2026-10/` | Recon pass 1: the control eval, public summary (plan, results, drill rounds, distillation) |
| `recon/` | Recon pass 2: `anthropic/` (A1–A7), `hosts/` (A8, A9), `coverage/` (N3–N5 live probe), `arr-domains/` (B5, B6), `census/`, `splits/` (01–14), `questions-for-list-authors.md`, `suspected-bugs.md` |
| `carried-forward.md` | Open 1.x issues and backlog items, kept as spec inputs |
| `../handoffs/` | Session handoffs (this repo's lane) |

## Conventions

- Absolute dates only. Authorship stamp under every doc title.
- Publish hygiene: this folder is public.
- Stages that don't apply are named here. All five apply.
