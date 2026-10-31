# dev — start here

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: LIVING (update in the same commit as any move or new lane). The shared dev lane for both
maintainers and their agents. This folder is public, so keep machine paths, emails and credentials out
of it.*

## What's live

**The 2.0 rebuild.** The 1.x skills are being rebuilt from the control eval's findings. Its router is
[`rebuild/INDEX.md`](rebuild/INDEX.md), which gives the stage, the gate decisions and where each document
lives. **The latest handoff in [`handoffs/`](handoffs/)** says where the last session stopped.

## Map

| Path | What | Class |
|---|---|---|
| [`handoffs/`](handoffs/) | one handoff per session, all eras, newest by date. Check both authors | ARCHIVE each |
| [`rebuild/`](rebuild/) | the 2.0 project: `INDEX`, `intent`, `recon`, `decisions`, then `spec` and `plan` | mixed |
| [`rebuild/eval-2026-10/`](rebuild/eval-2026-10/) | the control eval of 1.10.0 against ARR's handmade patches (recon pass 1) | ARCHIVE |
| [`rebuild/recon/`](rebuild/recon/) | recon pass 2: `anthropic/` (A1–A7), `arr-domains/` (B5, B6), `census/`, `splits/` (01–14), the questions doc, the suspected bugs | mixed |
| [`rebuild/carried-forward.md`](rebuild/carried-forward.md) | open 1.x issues and backlog items, kept as spec inputs, plus new small follow-ups | LIVING |
| [`reference/reqtificator-rules.md`](reference/reqtificator-rules.md) | the Reqtificator rulebook | LIVING |
| [`archive/`](archive/) | 1.x build material, frozen (see its README) | ARCHIVE |

## What lives elsewhere

- **The eval and census harness** (scripts, raw dumps, live-read spills, transcripts) is a private local
  repo, `arr-requiem-eval`, kept next to this repo's checkout. It holds machine paths and raw content, so it
  is never published. The docs here cite it as `census/splits/…` or `runs/…`.
- **ARR list bugs** found by the recon go to the ARR release hub's `BUGS.md` once they are re-verified.
  `rebuild/recon/suspected-bugs.md` is the candidate list.
- **The skills themselves:** `../authoria-requiem/`. Authoring guidance: the root `CLAUDE.md`.

## Conventions

- An authorship stamp goes directly under every title: `*Author: <Aaron|Heisen> (+ Claude session) · YYYY-MM-DD*`.
- LIVING v ARCHIVE is defined in the root `CLAUDE.md` (Doc classes). Supersede ARCHIVE docs; don't edit them.
- Land `dev/` docs on `main` at every stage gate, through a docs PR, so work never sits unseen on a long
  branch.
