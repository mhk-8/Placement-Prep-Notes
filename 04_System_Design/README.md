# 04 — System Design

> **These are detailed notes, not revision cards.** Every topic here is written to be *learned from*, with mechanisms explained, numbers attached, and trade-offs argued rather than listed. The one-page recall layer lives in `09_Cheatsheets`.
> **Last filled: 2026-09-18.**

## Why this folder is different

DSA and core CS have right answers. System design does not — it has **defensible answers**. The round is graded on how you reason: whether you gather requirements before designing, whether your numbers are self-consistent, whether you name the trade-off you are making, and whether you can defend a choice when the interviewer pushes back.

That means memorising a diagram of Twitter's architecture is close to worthless. What transfers is the **method** plus a deep enough understanding of the building blocks that you can reassemble them for a problem you have never seen.

## Structure

| Folder | What it is | When to use it |
|---|---|---|
| `01_Fundamentals` | The building blocks, each explained in depth: load balancing, caching, databases, replication, partitioning, consistency, queues, rate limiting, consistent hashing, probabilistic structures, microservices, observability | Learn these **first**. Every case study is an assembly of them |
| `02_LLD_OOD` | Low-level design: the method, SOLID applied, and five fully worked designs with class diagrams and code | **Most likely round for a fresher.** Do this before HLD |
| `03_HLD_Case_Studies` | Eleven case studies, each end to end: requirements → estimates → API → schema → architecture → deep dives → bottlenecks | After fundamentals. One studied properly beats ten skimmed |
| `04_Design_Patterns` | Creational, structural and behavioural patterns with real usage and downsides | Feeds directly into LLD rounds |

Top-level files:
- **`framework.md`** — the interview procedure, minute by minute, with what to say at each stage
- **`estimation-numbers.md`** — the latency table, capacity arithmetic, and five worked estimations
- **`glossary.md`** — every term you need to use precisely

## Reading order

1. `framework.md` — so everything else has a place to hang
2. `estimation-numbers.md` — the numbers make the rest concrete
3. `01_Fundamentals` in order, 01 through 11
4. `02_LLD_OOD`, framework then the case studies
5. `04_Design_Patterns` (short, feeds LLD)
6. `03_HLD_Case_Studies`, at least four written up in your own words

## Priority for a fresher

| | Weight for 0–2 YOE | Note |
|---|---|---|
| **LLD / machine coding** | **High** | The likeliest design round at product companies |
| **Fundamentals vocabulary** | **High** | You need it even in a DSA round follow-up |
| Design patterns | Medium | Asked directly in MCQs and implicitly in LLD |
| **HLD case studies** | Medium | Increasingly asked even of freshers, usually lighter |

If time is short, `02_LLD_OOD` and `01_Fundamentals` are the two that pay. `03_HLD_Case_Studies` is P3 in `00_Start_Here/syllabus-checklist.md` for SDE freshers — but read `framework.md` and two case studies regardless, because a lighter HLD question can appear anywhere.

## How to actually study this

**Do not read passively.** For each case study:
1. Cover the notes and spend 10 minutes designing it yourself on paper.
2. Read the notes and mark every decision you missed.
3. Write your own one-pager in `03_HLD_Case_Studies/<case>/my-version.md`.
4. Explain it aloud to someone, or to a recording, in 5 minutes.

Step 4 is the one people skip, and it is the one the round actually tests. You are being graded on a **spoken** performance, so rehearse the spoken form.

## The five things that lose this round

1. **Designing before clarifying.** Jumping to a diagram in minute two.
2. **Numbers that do not add up.** Claiming 1M QPS and then a single Postgres instance.
3. **Naming a technology instead of a mechanism.** "We'd use Kafka" without saying what property of Kafka you need.
4. **No trade-off.** Every choice costs something; say what.
5. **Silence.** The interviewer cannot grade thinking they cannot hear.
