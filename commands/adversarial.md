---
description: Spawn two peer lead agents that adversarially critique and improve an artifact until convergence
argument-hint: <task_or_artifact_description> [--rounds=N] [--max-rounds=N] [--domain=<hint>] [--rubric-file=<path>] [--git-versioned] [--dual-owner] [--verbose] [--help[=<topic>]]
---

# Adversarial Peer Review Loop

Orchestrate a symmetric adversarial loop between two peer lead agents (Peer A and Peer B) who iteratively critique and improve a shared artifact until they converge on a result neither can meaningfully improve further.

This is **NOT** a generator/critic split. Both peers are equals. Both criticize. Both propose changes. Both must defend their choices. Deference is failure. Consensus reached without friction is suspicious.

## User input

$ARGUMENTS

## Help mode (intercept first)

Before doing anything else, check if the user passed `--help` or `-h` in `$ARGUMENTS`.

- If `--help` or `-h` alone: print every section below that carries a `<!-- help:* -->` marker at its top, in the order they appear in this file. Do NOT run pre-flight. Do NOT spawn peers. Exit after printing.
- If `--help=list`: print only the list of available help topics — i.e. the tag names of every `<!-- help:* -->` section, one per line, with a one-line summary taken from the section's first sentence. Exit.
- If `--help=<topic>`: print only the section tagged `<!-- help:<topic> -->`. If no such topic exists, print the available list and exit.

All content wrapped between a `<!-- help:<topic> -->` marker and the next `##` heading (or the next `<!-- help:* -->` marker, whichever comes first) is help content.

Sections in this file that do NOT carry a `<!-- help:* -->` marker are agent-only instructions and must never be emitted by help mode.

## Flags

<!-- help:flags -->
**Available flags and defaults:**

- `--rounds=N`: minimum rounds before early exit is allowed. **Default: 3**
- `--max-rounds=N`: hard cap on rounds regardless of convergence state. **Default: 12**
- `--domain=<hint>`: optional domain hint. If provided, triggers a domain extraction step that lightly orients both peers with complementary leans. If absent, peers run as generic senior leads with identical prompts. **Default: none**
- `--rubric-file=<path>`: path to a markdown file containing a custom critique rubric, pasted verbatim into both peers' prompts. See `--help=rubric-format`. **Default: none**
- `--git-versioned`: enable git branching. Without `--dual-owner`: single branch `adversarial/run-<ts>`, peers ping-pong commits. With `--dual-owner`: split branches `adversarial/peer-a` and `adversarial/peer-b` from a shared base. If not in a git repo: warn, drop the flag, fall back to in-place shared artifact. **Default: off**
- `--dual-owner`: each peer maintains its own working copy. Without git, two in-memory copies diffed at the end. With git, see above. **Default: off (single shared artifact)**
- `--verbose`: print full critique payloads per round instead of just deltas and verdicts. **Default: off**
- `--help`, `-h`, `--help=<topic>`, `--help=list`: print documentation and exit without running.

Conflicting or malformed flags cause the run to stop and ask the user before spawning anything.

## Pre-flight

1. **Identify the artifact.** From `$ARGUMENTS`, extract what is being reviewed: a file path, a directory, a spec, a piece of prose, an idea. If ambiguous, ask the user exactly once before proceeding.
2. **Domain extraction (only if `--domain` is set).** Before spawning peers, you (the orchestrator) produce a short domain-specific framing to orient them. Both peers receive it identically; the only asymmetry is the `lean` field. Output of this step is a small JSON object:
   ```json
   {
     "domain": "<the hint, normalized>",
     "key_concerns": ["...", "...", "..."],
     "failure_modes_to_hunt": ["...", "..."],
     "peer_a_lean": "<one sentence: which aspect A should weight slightly heavier>",
     "peer_b_lean": "<one sentence: which aspect B should weight slightly heavier, complementary to A>"
   }
   ```
   The two `lean` fields must be complementary, not opposed (e.g. "correctness" vs "maintainability", not "speed" vs "quality"). If you can't write two genuinely complementary leans for the given domain, drop the leans and run peers as identical generics.
3. **Rubric loading.** If `--rubric-file` is set:
   - Read the file. If it does not exist, stop and tell the user.
   - If shorter than 10 non-empty lines, warn the user that the rubric looks thin and ask whether to proceed.
   - Hold the rubric text for peer prompts. If both `--domain` and `--rubric-file` are set, both apply: rubric is authoritative, domain framing is orientation.
4. **Git check.** If `--git-versioned`:
   - Verify `git rev-parse --is-inside-work-tree` returns true.
   - Verify working tree is clean (or warn and ask to proceed).
   - Create base branch/tag `adversarial/base-<timestamp>`.
   - In `--dual-owner` mode, create `adversarial/peer-a` and `adversarial/peer-b` from base.
   - In single-artifact mode, create `adversarial/run-<timestamp>` from base.
   - If not in a repo: warn, drop the flag, continue with shared artifact.
5. **State the plan in one paragraph** to the user: artifact, domain (if any), peer leans (if any), rubric source, rounds, mode. Then start.

## The loop

For round `r` from 1 to `--max-rounds`:

### Turn A (Peer A)

Spawn Peer A via the Task tool with a subagent prompt containing:
- The current artifact state
- The history of prior rounds (verdicts + diffs only, not full artifacts)
- The rubric (custom if provided, else the generic rubric below)
- The domain framing if `--domain` was set, including Peer A's lean
- Explicit instruction:

  > You are Peer A. You are a senior lead with strong opinions and no diplomatic filter. Peer B is your equal, not your subordinate, not your target. You will critique the current artifact and propose concrete changes. If the artifact is good, say so plainly and explain why — but look hard first. Do not hedge. Do not pad. Your output MUST follow the handoff format exactly.

Peer A's output format (strict JSON):

```json
{
  "round": <r>,
  "peer": "A",
  "verdict": "needs_work" | "acceptable" | "excellent",
  "blocking_issues": [
    {"severity": "high|medium|low", "issue": "...", "evidence": "...", "proposed_fix": "..."}
  ],
  "suggestions": [
    {"issue": "...", "proposed_fix": "..."}
  ],
  "changes_applied": "<diff or description of what Peer A modified this turn>",
  "defense_of_prior_choices": "<if B criticized something in the previous round and A disagrees, defend here; otherwise omit>",
  "converged": <bool — true only if A believes no further meaningful improvement is possible>
}
```

Apply Peer A's `changes_applied` to the artifact per the mode (shared / ping-pong branch / A's branch / A's working copy).

### Turn B (Peer B)

Spawn Peer B symmetrically, but:
- Peer B sees A's latest output and applied changes
- Peer B's lean (if any) is different from A's
- Same output format, with `"peer": "B"`
- Same instruction framing, swapping A and B

Apply Peer B's changes the same way.

### Convergence check

After both turns in round `r`:

- **Early exit** only if: `r >= --rounds` AND **both** peers returned `converged: true` AND neither peer raised `blocking_issues` with severity `high` or `medium`.
- Otherwise continue to round `r+1`.
- At `r == --max-rounds`, stop and mark result **"converged by timeout"**.

### Anti-loop guards

- **Stagnation**: if `r >= 4` and the last 3 rounds show no net changes (empty or trivially equivalent diffs), force-exit with status **"stagnant"**.
- **Oscillation**: if `r >= 3` and the same blocking issue has been raised and "fixed" and re-raised across rounds, surface it to the user as a **"genuine disagreement"** and ask them to arbitrate. Do not let the peers oscillate.
- **Capitulation detection**: if a peer's verdict flips from `excellent` to `needs_work` (or vice versa) without substantive new evidence, flag it. This is theater, not review.

## Final report

On exit, produce:

1. **Status**: `converged | timeout | stagnant | arbitrated`
2. **Rounds used**: `r / max`
3. **Final artifact**: path or inline depending on size. In `--dual-owner` without git, show both versions side-by-side. In `--dual-owner` with git, list the two branches.
4. **Round-by-round summary**: one line per round with both verdicts and diff stats (e.g. `R3: A=needs_work (+12/-4), B=acceptable (+3/-1)`).
5. **Unresolved disagreements**: list them plainly. Do not paper over.
6. **If `--git-versioned` + `--dual-owner`**: show `git diff peer-a peer-b`, call out the divergent hunks, and suggest a merge/cherry-pick path. The final merge decision is the user's.

## Generic rubric (used when no `--rubric-file`)

- Is it correct? Is it clear? Is it complete relative to its stated purpose?
- Is there unjustified complexity? Missing edge cases? Sloppy reasoning?
- Would a skeptical expert in the relevant field accept this?
- What would you change if you owned this and had to maintain it?
- What is the strongest objection someone who disagrees with the current approach would raise?

## Guardrails

- **Never** let the peers converge in round 1. Round 1 is for finding what's not obvious. Even if both peers want to declare convergence, force at least round 2.
- **Never** summarize a peer's output — pass it through. The value is in their specific language and framing.
- **Never** inject your own opinions (as the orchestrator) into the peer prompts mid-loop. You are a referee, not a third peer. The domain extraction step at pre-flight is the only exception, and it happens once before the loop starts.
- If a peer produces output that doesn't match the handoff format, re-prompt once. If it fails again, stop the loop and report the malfunction.

## Overview

<!-- help:overview -->
**What this command does.** Spawns two equal-rank "senior lead" agents (Peer A and Peer B) and runs them through an adversarial critique loop against a single artifact — code, prose, spec, design, whatever. Each round: A critiques and edits, then B critiques A's work and edits, then convergence is checked. The loop exits when both peers agree no meaningful improvement is left (and at least `--rounds` rounds have passed), or when it hits `--max-rounds`, stagnates, or oscillates.

**When to use it.** When you want something pressure-tested harder than a single pass can do, and you don't want to manually engineer the back-and-forth yourself. Good for: final-pass code review before shipping, prose that has to hold up (specs, proposals, sensitive comms), designs where you suspect the first draft is only "fine".

**When NOT to use it.** Tasks with a clear single correct answer (lookups, math, trivial refactors). Anything under ~50 lines or a few paragraphs — the loop overhead isn't worth it. Anything where the failure mode is "I don't know enough" rather than "this could be better" — more agents don't create more knowledge.

## Rubric file format

<!-- help:rubric-format -->
**The rubric is free-form markdown pasted verbatim into both peers' prompts**, so it must be written for a lean, sharp reader — not as a manifesto.

What works well:
- Short declarative criteria or pointed questions, not prose paragraphs.
- Grouped under a few clear aspect headings (e.g. `## Correctness`, `## Clarity`, `## Performance`).
- Each item is something a reviewer can concretely check against the artifact.
- Optional "Out of scope" section to prevent wasted rounds on nitpicks you don't care about.

What doesn't work:
- Long prose explanations of *why* each criterion matters. The peers don't need to be convinced; they need to know what to check.
- Overlapping or fuzzy criteria that produce redundant blocking issues every round.
- Generic "be thorough, be rigorous" filler — that's already in the peer prompts.

Minimal example (`rubric-security-review.md`):

```markdown
# Security review rubric

## Threat model
- Is the threat model stated explicitly somewhere?
- Are trust boundaries drawn? What crosses them?
- Which actors are assumed trusted, and why?

## Input handling
- Every input source enumerated?
- Validation at the trust boundary, not scattered?
- What happens on malformed/oversized/malicious input?

## Secrets and crypto
- Any secret in source, config committed to repo, or env var leaked to logs?
- Any hand-rolled crypto? Any deprecated primitives?
- Key rotation story?

## Dependencies
- Pinned? Audited? Update path documented?

## Out of scope
- Performance micro-optimizations.
- Code style beyond what affects auditability.
```

A rubric can be 15 lines or a few hundred. Density matters more than length. If peers keep raising issues outside the rubric, it's too narrow; if they converge trivially, it's too loose.

## Modes explained

<!-- help:modes -->
**Single-artifact (default).** One artifact, in place. Peers take turns editing it. Simple, fast, no git required. Good for most cases.

**Single-artifact + `--git-versioned`.** Same as above but each peer's turn becomes a commit on branch `adversarial/run-<ts>`. You get a clean history of who changed what when. Good when you want to inspect the loop's evolution afterward or cherry-pick specific rounds.

**`--dual-owner` alone.** Two in-memory working copies, one per peer. Each peer only edits their own copy but sees the other's output. Final report shows both side-by-side. Good when you want to see genuine divergence — what would A ship vs what would B ship.

**`--dual-owner` + `--git-versioned`.** Two branches (`adversarial/peer-a`, `adversarial/peer-b`) from a shared base. Each peer commits on their own branch. Final report runs `git diff peer-a peer-b` and guides you through a manual merge. Most work, most tracked. Good for high-stakes artifacts where you want to reconstruct the debate later.

**Warning.** `--dual-owner` without git means you get two final versions and must pick/merge them yourself from inline output. Fine for short artifacts, painful for anything large.

## Usage examples

<!-- help:examples -->
**Simplest.** Pressure-test a piece of prose:
```
/adversarial review the README in this repo
```

**With a domain hint.** Orients the peers toward complementary security perspectives:
```
/adversarial audit src/auth/ for vulnerabilities --domain=security
```

**With a custom rubric.** Full control over what the peers check:
```
/adversarial review the API spec --rubric-file=./rubrics/api-review.md
```

**Tight budget.** For a small artifact, keep the loop short:
```
/adversarial clean up this config file --rounds=2 --max-rounds=4
```

**Full treatment for a high-stakes artifact.** Dual-owner, git-versioned, domain-oriented, custom rubric:
```
/adversarial the deployment runbook before go-live --dual-owner --git-versioned --domain=ops --rubric-file=./rubrics/runbook.md --max-rounds=8
```

**Get help:**
```
/adversarial --help
/adversarial --help=list
/adversarial --help=rubric-format
```

## Troubleshooting

<!-- help:troubleshooting -->
**Peers converge immediately in round 2.** Either the artifact really is solid or the rubric is too loose. Try a stricter rubric, a domain hint, or bump `--rounds` to force more iterations.

**Peers never converge, hit max-rounds.** The artifact has genuine ambiguity or the rubric is too broad and generates infinite valid critiques. Check the unresolved disagreements in the final report — often the real answer is "the peers found something you need to decide, not something they can fix."

**Oscillation (same issue fixed and re-raised).** This is surfaced as "genuine disagreement" and the command will ask you to arbitrate. Don't try to fix it by adjusting the rubric — the disagreement is usually substantive.

**Stagnation (no net changes over 3 rounds).** The peers are out of ideas. Accept the current state as the convergent result. If you think more should be possible, provide a narrower rubric focused on the aspects you still care about.

**One peer keeps flipping verdicts without new evidence.** Flagged as "capitulation theater". Usually means that peer's prompt framing is too weak — the lean or rubric isn't giving it enough to defend. Try a more opinionated rubric or a more specific `--domain` hint.

**Git mode fails silently.** You're probably not in a repo, or the working tree wasn't clean. Pre-flight should warn but if it didn't, check `git status` before re-running.
