# Rationale

> Why the manifesto is structured the way it is. Design reasoning, not legal advice.

This document explains the design choices behind the role split. It exists so contributors and adopters understand which decisions are essential and which are stylistic.

---

## A note before reading

Some of the reasoning below touches on copyright and authorship of AI-assisted work. **This is not legal advice.** The legal treatment of AI-assisted creative work is unsettled in most jurisdictions and actively evolving. The manifesto's role architecture is one practical attempt to preserve human creative control over the work — it does not guarantee any specific legal outcome. Consult an actual lawyer for actual claims.

---

## Why three roles, not one

The split into **Operator / Architect / Executor** could look like over-engineering for a solo founder. It exists for two reasons.

### Reason 1 — Operational clarity

A solo founder working with multiple AI tools at once tends to blur their purpose. Asking Claude.ai to write production code at 2 a.m. without planning, or asking Claude Code to make architectural decisions on the fly, are both common failure modes. The three roles give each interaction a named purpose:

- **Architect** plans and prompts; does not touch files.
- **Executor** executes prompts; does not invent architecture.
- **Operator** decides, validates, and bridges.

This is the practical reason. It would justify the split on its own.

### Reason 2 — Human authorship

The second reason is structural. In the United States, copyright protection requires **human authorship**. Courts (Thaler v. Perlmutter, D.C. Circuit 2025; SCOTUS declined to hear the appeal in 2026) and the U.S. Copyright Office (Copyright and Artificial Intelligence, Part 2, January 2025) have held that:

- Works generated autonomously by AI are not eligible for copyright.
- Prompts alone do not give the user authorship — they function as "unprotectable ideas" or "instructions."
- Copyrightability of AI-assisted work depends on **creative control** by the human: direction, prompting, curation, and revision sufficient to make the resulting expression a product of the human's mind.

Outside the U.S. the picture differs. The EU, UK, China, and other jurisdictions have their own evolving rules. Common to most is some version of the human-control question, but specifics vary widely. **This area is, as of this writing, a grey zone in most jurisdictions.**

Against that backdrop, the three-role split is a deliberate attempt to preserve and document human creative control:

- The **Operator** is the human who decides what is built, why, and whether the output is acceptable. The creative control resides here.
- The **Architect** helps formulate the plan and the prompts — i.e., the "ideas" and "instructions" that the Copyright Office considers unprotectable on their own. The Architect does not decide what gets built.
- The **Executor** generates output from explicit prompts. The Operator reviews and approves. The Executor is a tool.

The paper trail this produces — session logs in `docs/sessions/`, prompts retained in `TODO.md` and chat history, decisions recorded in handoff notes, commits authored by the Operator — is the kind of documentation that helps make the case that the work was created under human direction, not autonomously by AI.

Whether this is sufficient for copyright in any specific case is for courts and copyright offices to decide. The manifesto does not promise an outcome. It is a workflow designed to be defensible if the question arises — and useful even if it never does.

---

## What this manifesto is not

- It is not a legal framework.
- It is not a substitute for licensing, contracts, or counsel.
- It does not guarantee that any specific work will or will not be copyrightable.
- It does not address employment, contractor, or joint-authorship questions when more than one human is involved.

It is a workflow. The fact that some of its choices are informed by legal context does not make it legal advice.
