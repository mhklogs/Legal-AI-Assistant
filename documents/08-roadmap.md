# Legal AI Assistant — Delivery Roadmap (v3)

> **Provenance note.** This roadmap was produced on **2026-09-29** from the same
> static analysis as the rest of `documents/` (see `00-index.md`). Backlog items are
> derived from the functional requirements in `02-functional-requirements.md`, the
> non-functional targets in `03-non-functional-requirements.md`, and the market
> findings in `01-market-analysis.md`. Timeline targets are `[TO BE VALIDATED]`
> where they depend on future estimates rather than shipped code.

## 1. Objective & horizon

Legal question-answerer with citation support. This roadmap plans the next **5–6 weeks** of incremental delivery in
lockstep with the SDLC phases and traceability rules in `07-sdlc-lifecycle.md`
(Requirements → Design → Implement → Verify → Release/Operate → Improve).

Current shipped state: no public deployment yet (`—` in the repo facts); the
single-file Gradio application (`copy_of_legal_ai_assistant.py`) is committed and
the v2 documentation set is complete. Shipped behaviour today: document-grounded
legal Q&A scoped to Pakistani law, PDF/image ingestion via PyMuPDF and Tesseract,
multi-turn history, and PDF export.

## 2. Product backlog

Prioritised with MoSCoW. Items are phrased as outcomes (not tasks) and map to FR/NFR ids.

| ID | Item (outcome) | Source | Priority |
| --- | --- | --- | --- |
| PBI-01 | Every answer cites the uploaded document passage it was grounded on, so a user can verify it | FR-1 (page behaviour) / market gap | Must |
| PBI-02 | Uploaded PDFs and images are validated and rejected with a clear message when extraction yields nothing usable | FR-8, NFR-5.4 | Must |
| PBI-03 | A missing or empty `GROQ_API_KEY` stops startup with a clear, actionable error | FR-8 | Must |
| PBI-04 | The app runs as plain Python without the Colab `!pip install` magic, so it is executable outside a notebook | FR-1 | Must |
| PBI-05 | Hand-written use cases replace the "no use cases could be derived" placeholder, covering ask, upload, export, and reset | FR-1, `05-use-cases.md` | Must |
| PBI-06 | Provider errors and timeouts surface as a typed error in the chat transcript, not a stack trace | NFR-4.1 | Should |
| PBI-07 | An auth or session boundary replaces the public Gradio share link, which currently exposes the full legal transcript | NFR-5.5, NFR-5.7 | Should |
| PBI-08 | Automated tests cover the extraction path and the chat-history reset behaviour | NFR-6.1 | Should |
| PBI-09 | CI runs a smoke import plus lint on every push | NFR-6.3, NFR-6.2 | Should |
| PBI-10 | Dependency vulnerability scan run and recorded (`pip-audit`) | NFR-5.3 | Should |
| PBI-11 | Retrieval over Pakistani statute replaces prompt-only grounding, so answers rest on cited primary law | market differentiator (§6) | Could |
| PBI-12 | Rate limiting and HTTPS/HSTS verified once a hosted target exists | NFR-5.5, NFR-5.6 | Won't (this horizon) |

## 3. Sprint plan

**Sprint cadence:** 1 week = 1 sprint; stand-up daily (15 min), sprint review + retrospective at the end of each sprint.

| Sprint | Goal | PBI delivered | Done/exit criteria | Phase (SDLC) |
| --- | --- | --- | --- | --- |
| Sprint 1 | Make the app runnable outside Colab and fail loudly on bad input | PBI-03, PBI-04, PBI-02 | `python copy_of_legal_ai_assistant.py` starts on a clean machine; empty upload rejected with a message | Implement → Verify |
| Sprint 2 | Ship citation support — the product's headline promise | PBI-01 | every answer carries a source reference into the uploaded document | Implement → Verify |
| Sprint 3 | Write the use cases the static pass could not derive | PBI-05 | `05-use-cases.md` has hand-written ask / upload / export / reset flows, each anchored to the module that implements it | Requirements → Design |
| Sprint 4 | Close the exposure the README already flags | PBI-06, PBI-07 | share link no longer carries a full transcript without a session boundary; provider failure returns a typed error | Verify |
| Sprint 5 | Test, pipeline, and scan | PBI-08, PBI-09, PBI-10 | CI green on every push; `pip-audit` result recorded in NFR-5.3 | Verify → Release |
| Sprint 6 | Validate the retrieval direction and cut a release | PBI-11 spike, backlog refinement | spike report decides build-vs-defer; use-case walkthrough updated; release cut | Release & Operate → Improve |

## 4. Ceremonies

- **Daily stand-up (15 min):** what shipped since yesterday, what's blocked, what's next — tied to the active sprint's PBI board.
- **Sprint review (30 min, end of sprint):** demo PBI outcomes against the sprint goal; update `05-use-cases.md` walkthrough where behavior changed.
- **Retrospective (30 min, end of sprint):** inspect + adapt; record one actionable improvement per sprint in git notes.
- **Backlog refinement (before sprint 1):** re-prioritise PBIs against latest market findings.

## 5. Burndown (planned)

Tracked as PBI points remaining per sprint. Planned trajectory below; the team records actuals at each sprint review. `[TO BE MEASURED]` until the first sprint completes.

| Sprint | Planned remaining points |
| --- | --- |
| Start | 13 |
| Sprint 1 | 11 |
| Sprint 2 | 9 |
| Sprint 3 | 7 |
| Sprint 4 | 5 |
| Sprint 5 | 2 |
| Sprint 6 (Done, 0) | 0 |

## 6. Rollout & deploy

- Build/deploy per `07-sdlc-lifecycle.md` §5 (release policy).
- Production: none yet (`—`). No Vercel configuration or container definition is
  present; the current distribution is a Gradio `share=True` link, which the README
  already warns exposes the transcript. Sprint 1–4 remove that exposure before any
  hosted target is chosen.
- Health: a broken build blocks the next sprint's first commit; security findings are release blockers.

## 7. Risks

| Risk | Mitigation |
| --- | --- |
| Requirements drift vs. implemented code | PBI↔FR↔use-case traceability check per change (`07-sdlc-lifecycle.md` §3) |
| Unmeasured NFRs treated as done | `[TO BE MEASURED]` targets stay visible until instrumented |
| Burndown actuals fall off plan | Over-plan cut scope in the retrospective, not mid-sprint |
| Confident-sounding wrong answers presented as legal expertise | Keep the "demonstration, not a legal advice service" limitation prominent in the README until PBI-01 lands |
| Category commoditized by Harvey / Lexis+ AI / CoCounsel | Differentiate on Pakistani-law grounding and verifiable citations, not price (`01-market-analysis.md` §7) |
