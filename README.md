### Martín Maradei

Chemical engineer (FIUBA, thesis stage) building and evaluating LLM systems in production.

**Most of my work lives in private repositories** — production systems for the Buenos
Aires City Government and institutional platforms that hold data on schools, staff and
students. They are not mine to publish. The contribution graph is real; the code behind
it is under NDA by nature.

Here is what is in them.

**Education management platform** · React 19 · TypeScript · Supabase · Deno
129 PostgreSQL migrations, 11 edge functions, 191 test files. A 21-step ETL out of Google
Sheets sitting behind ~280 integrity assertions generated from the data catalogue rather
than hand-written — 88 parity failures closed by fixing the pipeline, never by relaxing
an assertion. Row-level security per role, column-level security over personal data,
and 40 internal functions that were callable unauthenticated found and closed.

**Two LLM assistants, behind guardrails that are tested**
A deterministic classifier rejects PII requests, raw SQL and instruction overrides before
the model is called; a fail-closed server-side post-check catches the rest. Both are gated
by a 90-case evaluation harness across golden and adversarial datasets — prompt injection,
cross-role access, PII, out-of-manual — with deterministic scorers and no LLM-as-judge.
Golden ≥90% and adversarial 100% are enforced thresholds; a failure exits non-zero.
The scorers fail a *paraphrased* refusal on purpose: the canonical copy has to come from
the guardrail, not from the model.

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

📫 maradeimartin@gmail.com · [LinkedIn](https://linkedin.com/in/martin-maradei)
