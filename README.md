### Martín Maradei

Chemical engineer (FIUBA, thesis stage) building and evaluating LLM systems in production.

**What's public:** [deterministic-evals](https://github.com/Tyran-byte/deterministic-evals)
— the core of the evaluation harness that gates my production merges, extracted and
open-sourced. Four deterministic scorers, no LLM-as-judge, and an exit-code contract that
tells a real regression apart from an inconclusive run. Zero dependencies, 38 tests.

**Most of my work lives in private repositories** — production systems for the Buenos
Aires City Government and institutional platforms that hold data on schools, staff and
students. They are not mine to publish. The contribution graph is real; the code behind
it is under NDA by nature.

Here is what is in them.

**Education management platform** · React 19 · TypeScript · Supabase · Deno
In production since August 2026, serving ~600 users across 5 roles. 141 PostgreSQL
migrations, 11 edge functions, 214 test files. A 23-step ETL out of Google Sheets sitting
behind ~280 integrity assertions generated from the data catalogue rather than
hand-written — 88 parity failures closed by fixing the pipeline, never by relaxing
an assertion. Row-level security per role, column-level security over personal data,
and 40 internal functions that were callable unauthenticated found and closed.

**Two LLM assistants, behind guardrails that are tested**
A deterministic classifier rejects PII requests, raw SQL and instruction overrides before
the model is called; a fail-closed server-side post-check catches the rest. Both are gated
by a 103-case evaluation harness across golden and adversarial datasets — prompt
injection, cross-role access, PII, out-of-manual — with deterministic scorers and no
LLM-as-judge. Golden ≥90% and adversarial 100% are enforced thresholds; a failure exits
non-zero. The scorers fail a *paraphrased* refusal on purpose: the canonical copy has to
come from the guardrail, not from the model. The harness core is the public repo above.

**Local-first voice agent** · Python · asyncio
~6,200 lines, 53+ tests. Failover across 5 LLM providers with exponential cooldowns,
a 3-layer memory (RAM, episodic SQLite with embeddings and hybrid FTS5 search, versioned
YAML profile), and a token→sentence→TTS streaming pipeline. Runs local models via Ollama,
with real VRAM measurement per model.

**Personal AI infrastructure** · Cloudflare Workers · MCP
A remote MCP server in production on Cloudflare Workers, exposing my private repos as
tools for claude.ai and cloud routines — timing-safe dual auth, gated writes, and a
security audit applied before deploy. Plus a tri-CLI orchestration layer: Claude, Codex
and Grok deliberating on real architecture decisions through a shared board, with
schema-validated votes and vetoes that changed the outcome.

**Legacy platform still in production** · Google Apps Script
~40,000 lines serving 550+ users across ~1,200 schools. 21 tables, 59+ API endpoints
across 5 roles, a data access layer with pessimistic locking, and a 5-step bidirectional
sync pipeline.

**Chemical engineering thesis** · Python · pytest
Crosslinked ion-imprinted polymers for selective rare-earth recovery from electronic
waste, at LabMOr-IQAI (FIUBA). The lab deliverables are under automated test — TGA curve
processing, peak extraction, column mass balance — which is the same discipline as the
production data gates, applied to a bench.
3rd place, Engineering category, AUGM Young Researchers' Conference 2025.

---

I value verification over confidence, evidence over intuition, and systems that fail
loudly rather than quietly.

📫 maradeimartin@gmail.com · [LinkedIn](https://linkedin.com/in/martin-maradei) · [What's public](https://github.com/Tyran-byte/deterministic-evals)
