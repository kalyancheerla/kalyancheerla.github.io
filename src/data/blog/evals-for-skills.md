---
title: "Evals for skills: are you doing it?"
author: Kalyan Cheerla
pubDatetime: 2026-06-07T09:00:00Z
description: Tests are to code what evals are to skills. Same job, different artifact. A short field guide to evaluating Claude skills with cassettes, graders, and a CI gate that doesn't lie.
tags:
  - ai
  - llm
  - skills
  - evals
  - claude
  - agents
  - mcp
  - workflows
---

> *How this got made: I sketched the structure and the takes, Claude helped me draft, I edited. Just so u know.*

```
🚀 TL;DR
U wrote a Claude skill. Cool. How do u know it still works tomorrow?

Tests are to code what evals are to skills. Same job, different artifact.

This is a short field guide:
  - what's in a skill (so u know what to test)
  - what to grade
  - how to replay tool calls so CI is stable / tool-deterministic
    (cassettes, yes like the VHS thing)
  - the whole loop in one diagram

(Heads up: skim-friendly. Scroll to the diagram if ur impatient.)
```

## Table of contents

---

## 🤔 Wait, evals for skills? are you doing it?

Honest question.

U wrote a skill last month. A markdown file, some frontmatter, a system prompt, a few MCP tool bindings, maybe a subagent. U tested it three times, the output looked right, u shipped it, marked the jira done, moved on.

Then last Tuesday u tweaked one paragraph in the prompt to fix a thing.

**Did the skill still work?**

Be honest. U ran it once on the input u fixed it for and called it a day, right?

Same. Me too. **That's the gap.** Skills look like prompts. They behave like services. We treat them like prompts. Hence: this post.

---

## 🧪 What's an eval?

An **eval** is just a test for an LLM-driven system.

```
eval = scored_run(system, fixed_input)  →  score
```

Think of it the same way you think about an integration test:

- Hand the system fixed inputs.
- Get outputs.
- Score the outputs against what you expected.
- Fail loud if the score drops.

The two annoying differences from a normal test:

- **No exact match.** LLMs are nondeterministic. You compare against a baseline with a tolerance band, not `assert eq`.
- **Less ground truth.** You're not testing a pure function, you're testing "did the model do the right kind of thing." Grade on properties, not equality.

---

## 🎒 What's a skill, actually?

Strip a Claude skill down:

```
skill = long-ass prompt + frontmatter + tools (MCPs, Search, Bash, etc.) + subagents
```

Each piece, briefly:

- **long-ass prompt:** the `SKILL.md` body. The actual instructions.
- **frontmatter:** `description` (so the LLM picks the right skill), `allowed-tools` (gating), name, version.
- **tools:** MCP servers, Search, Bash, file ops, whatever the skill is allowed to call. Usually *your* MCP (your DB, your auth) plus *external* ones (vendor APIs you don't own).
- **subagents:** mini-skills the main loop can delegate to.

If it helps the SWE brain: think of a skill like an **API endpoint** that does a bunch of retrievals and returns a result.

- The **prompt** is the handler logic.
- **Frontmatter** is the route decorator (URL, auth, docstring).
- **Tools** are the helper functions the handler calls to compute things.
- **Subagents** are async calls the handler fires off (think DB fetches, downstream services) to grab what it needs.

Glue eval and skill together and the **working definition** falls out:

```
skill_eval = scored_run(skill, fixed_input)  →  score_vector
```

Tests for code. Evals for skills. Same job.

---

## 💡 Why bother?

Four things you get:

- **Change the prompt fearlessly.** No more "I tweaked one line, hope nothing breaks."
- **Catch model regressions.** New model minor lands, ur eval tells u if ur skill still works on it.
- **Attribute red runs.** When prod misbehaves, u can isolate: was it the prompt, the model, the upstream data, or the user?
- **Ship faster.** Counterintuitive but true. Babysitting goes away when CI is trustworthy.

> **Real one on regressions:** Opus 4.7 made *effort* and *thinking* more configurable than 4.6. Adaptive thinking that Just Worked on 4.6 tanked retrieval on a skill of mine, recall dropped to zero on cases that were green the day before. Fix: pin effort to `xhigh`, or add more alias hints to the tool descriptions. Without evals I'd have blamed the prompt for weeks.

**Now the other side: evals aren't free.** Every PR run burns agent tokens (cassettes make the *tools* free, the *agent loop* still pays Anthropic). LLM-as-judge calls pile on. Stability checks multiply by N. Live re-recording costs real $. Budget for evals like u budget CI minutes, because that's what they are. Prompt-hash skip is the one thing keeping the bill in check, and it only helps when nothing about the skill changed.

**Plus the meta cost:** ur harness is a *simulation* of ur prod runtime (cowork, Claude Code, whatever). Parity drift between sim and real is constant work. And if u keep extending evals far enough (mine real artifacts, have users rate outputs, train on the deltas) congrats, u've accidentally reinvented RLHF. At which point real human feedback might just be the cheaper, better signal anyway. Worth knowing where the diminishing returns kick in.

---

## 📐 What do u grade it on?

Pick the axes that match what ur skill produces. For most "retrieval-and-synthesis" shapes (pull sources, write something) the **three core axes** are:

```
score_vector = retrieval + synthesis + structure  (+ soft axes)
```

Let's go one by one. This is the meat.

---

### 🎯 Retrieval: did the agent pull the right stuff?

```
recall    = |hit ∩ expected| / |expected|     ← "did u find all of them"
precision = |hit ∩ expected| / |hit|          ← "did u also drag in junk"
```

Mechanics:

- `expected` = ground-truth source IDs curated per fixture (you write these by hand once, per case).
- `hit` = the IDs the agent used. **Careful:** "used" splits into three sets and ur metric shifts with each:
  - `returned` (tool gave back) → `used` (agent fetched) → `cited` (in final artifact).
  - A skill can return 50, fetch 5, cite 2. Pick one, stay consistent. `cited` is usually the truest signal.
- Both numbers matter. **High recall + low precision** = pulled too much, dilutes synthesis, costs more. **High precision + low recall** = missed important context, will produce a confidently wrong answer.

Treat retrieval as the cheapest, most rigorous axis. It's set arithmetic on IDs. No fuzz, no judge, no LLM in the loop. If u only have time for one axis, start here.

---

### 💭 Synthesis: does the prose actually say the right things?

The one that actually matters most. Whatever the artifact looks like, this is what ur user reads. Two flavors:

**Keyfact matching** (rule-based, cheap, deterministic):

```
keyfact_match_rate = hit_keyfacts / required_keyfacts
anti_violations    = | anti_phrases ∩ output |       ← 0 is the only good number
```

For known facts (`"must mention strong growth"`, `"must not say 'reduce capex'"`), maintain a per-fixture list of keyfacts and anti-phrases. Plain string or regex matching. Boring, fast, reliable.

**LLM-as-judge** (for the parts u can't regex):

For fuzzier things ("does it capture the actual bear case", "is the recommendation directional"), a small model with a strict prompt can grade. Some rules to keep this honest:

- **Binary output, not floats.** Once u let the judge return a confidence score, u spend the rest of the project arguing about thresholds. Make it answer yes/no.
- **Pick the judge by calibration, not by size.** Same-as-evaluated shares blind spots, miscalibrated-smaller is worse than either. Cheap-and-calibrated > big-by-default.
- **One question per call.** Don't ask "is this output good in 5 ways". Ask 5 single-axis questions and aggregate the booleans.
- **Cache the judge calls.** Same `(judge_prompt, evaluated_output)` → same answer. Don't re-spend on the same comparison.

---

### 🏗️ Structure: does the artifact have the right shape?

```
hard_pass_rate = passed_gates / total_gates
```

Mechanics:

- Parse the output (HTML / JSON / markdown / whatever ur skill emits).
- Assert structural invariants in plain code: *"must have `<section id='summary'>`"*, *"timeline has ≥3 entries"*, *"every citation resolves to a real source ID"*.
- Each gate is binary: passed or failed. The rate is just the share that passed.

Structure matters less than synthesis (a beautifully-formatted answer that's *wrong* helps no one) but it's the easiest axis to wire up. **Don't ask an LLM if HTML is well-formed.** It will say yes on a malformed blob and mean it. Use the parser. Use it for things with a *right answer*: schema validity, required sections, link validity, citation resolution, table column counts, whatever.

---

### 🌡️ Soft axes: track now, maybe gate later

- **Trajectory:** sensible tool call order? Loops? Over-thinking?
- **Stability:** run the same prompt N times, measure variance. LLMs are nondeterministic, u want to know *how much* yours is.
- **Cost & latency:** tokens + seconds. Catches the silent *"added a subagent, now every run takes 3x longer."*
- **Citation correctness:** do the output's claims trace back to retrieved sources? Catches confabulation. Different from retrieval (which only checks if u touched the right docs, not if u quoted them faithfully).
- **Cassette-miss tally:** indirect drift signal. If misses climb, ur prompt is calling tools u've never recorded. Time to refresh fixtures.

Don't grade everything on day one. **Start with retrieval + one keyfact check + a couple of structure gates.** Add axes when something actually bites u.

---

### 📦 What a fixture looks like

To make this concrete, a single fixture is just a folder with the inputs + expected stuff:

```
fixtures/<case>/
  prompt.md             ← what to send the skill
  expected_sources.json ← ground-truth source IDs for retrieval
  keyfacts.json         ← strings that must appear in synthesis
  anti_phrases.json     ← strings that must NOT appear
  rubric.ts             ← structure gates (parser assertions)
  cassettes/            ← recorded MCP IO from the last live run
  gold.html             ← reference output, for human eyeballing
```

The graders only look at `expected_sources`, `keyfacts`, `anti_phrases`, and `rubric`. `gold.html` is just for u to skim when something goes weird. Cassettes are how u feed deterministic tool calls back in (next section).

> **Btw, why HTML and not Markdown for `gold.html`?** [It's the current format war](https://x.com/neetcode1/status/2056530113454583907). HTML wins render fidelity (u see exactly what the user sees), loses on safety (client-side JS in artifacts → XSS playground). Vim vs emacs energy. Pick a side, ship.

---

### 📋 Putting it together: what a graded run produces

One run, one fixture, one report:

```
fixture_report = {
  fixture_id,
  retrieval:  { recall, precision },
  synthesis:  { keyfact_match_rate, anti_violations, judge_passes },
  structure:  { hard_pass_rate, failed_gates: [...] },
  coverage:   { miss_count, misses: [...] },     ← cassette-miss tally
  trace:      { tool_calls, tokens, seconds },   ← soft, useful for triage
}
```

Aggregate across fixtures with a per-axis mean (or worst-case if ur paranoid). That's ur suite report.

---

### 🚦 Baseline + tolerance = the CI gate

A graded run is just numbers. To turn it into a *gate*:

1. **Lock a baseline.** Run the suite on a known-good version of the skill. Write the per-axis scores to `baseline.json`. That's ur green line.
2. **Pick a tolerance band.** Something like `±0.05` on rates, `0` on `anti_violations` and `failed_gates`. Tight enough to catch real regressions, loose enough to absorb LLM nondeterminism.
3. **Fail loud.** On every PR run, compare new vector to baseline. Drop in any axis beyond tolerance = CI red.
4. **Re-lock after intentional changes.** When u tighten the prompt and the new scores are *better*, update the baseline. Otherwise next week's PR thinks ur regressing.

> **Pro tip nobody talks about:** *hash-skip.* Hash everything that flips output (prompt, frontmatter, model version, runtime, cassettes). If it all matches the last green run, **skip the eval.** Saves most of ur token budget. Don't hash just the prompt though: the Opus 4.7 story above shows why.

---

## 📼 Cassettes for MCP

Here's the hard part.

You want to grade a skill *repeatedly*, but the skill calls MCP tools. If those tools return different data every run, you're grading "the skill + whatever Microsoft Graph felt like returning today." Not useful.

Three options:

- **Live in CI.** Cheap to set up, awful to live with. Corpus drifts. APIs rate-limit. Tokens expire on weekends. When a run goes red, you can't tell what broke. **Don't.**
- **Hand-mock each tool.** Deterministic, but drifts from the real tool surface immediately. You spend the rest of your life updating stubs. Hard pass.
- **Cassettes.** Call the real tool once, record request and response to disk, replay forever after. The skill thinks it's hitting the real MCP. It's hitting your tape.

**Cassettes win for almost everyone.**

> SWE analogy: this is **VCR for MCP**. Same record-and-replay pattern that Ruby's `vcr` gem, Python's `vcrpy`, and JS's `nock` use for HTTP. Just one layer up, at the tool-call layer instead of the network layer. The "cassette" name comes from those libraries. So yes, like the audio thing. Except instead of recording songs off the radio (or sneaking near the TV with a tape when one came on, the way my parents used to), you're recording `mcp__yourthing__search_pages({"query": "foo"})`.

How the harness works, in four moves:

1. **Spawn mock MCP servers** beside the skill. Same tool names, same schemas as prod. Handler looks up `(tool, args)` in `cassettes/` and returns what you recorded.
2. **First run: record.** Live MCP call. Pair gets written to disk.
3. **Every run after: replay.** Skill is none the wiser. CI is happy. Tool calls are free (agent tokens still aren't).
4. **On cassette miss:** **local:** return "no results", log it, iterate. **CI:** fail loud or gate on miss count, otherwise ur eval grades a degraded fake world and calls it green. Either way the miss is data: prompt drifted, fixtures need refreshing.

The external-MCP wrinkle: if it's *your* MCP backed by *your* DB, you have options (postgres dump, S3 cache, etc.). If it's a third-party MCP you don't own, cassettes are the only honest answer. Everyone at the agent layer hits this eventually.

---

## 🏗️ The whole thing in one picture

![Skill eval loop: fixtures feed a harness, mock MCPs replay cassettes, graders score the run artifact, and CI gates on the report.](/assets/images/skill-eval-loop.svg)

A page or two of code per box, honestly. Flow:

- Harness spawns the same MCPs the skill expects in prod.
- Handlers replay from disk instead of calling out.
- Agent loop runs as if live.
- U collect the full trajectory: every tool call, every result, the final output.
- Three graders score the pile. Report gets written. CI fails loud if any axis drops below baseline minus tolerance.

That's the loop. Re-runnable, **tool-deterministic** (cassettes pin IO, the agent still samples), cheap, and the run/grade split lets u iterate on *graders* without a fresh agent run.

---

## 👋 Closing thoughts

Btw, this isn't a finished story. The skill-eval space is *young af*. Plenty of open questions (frontmatter-fidelity, cassette key shape, eval portability across runtimes). I'll write more as I keep building.

If ur doing this differently at ur company, or u've got a sharper take on what "evaluating an agent" should mean, ping me. Always happy to compare notes.

Until then: **go grade ur skills.** Future-u will thank u.

Happy eval-ing 🥷, KC.

---

## 📖 Resources

- Ruby's [`vcr`](https://github.com/vcr/vcr): the OG record/replay gem. Source of the "cassettes" name.
- Python's [`vcrpy`](https://github.com/kevin1024/vcrpy): same idea, for HTTP.
- [Hamel Hussain on LLM evals](https://hamel.dev/blog/posts/evals/): canonical "how to think about this" reading.
- [Anthropic Agent SDK docs](https://docs.claude.com/en/docs/claude-code/sdk): for the runtime side.

Want to chat? [LinkedIn](https://www.linkedin.com/in/kalyancheerla/), [X DMs](https://x.com/kalyanch05), or hello@kalyanch.com.
