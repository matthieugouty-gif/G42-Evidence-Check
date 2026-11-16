# Product Requirements Document: Evidence Check

**Status:** Draft for discussion · **Author:** Matthieu Gouty · **Audience:** Robin, Mohammed Shayan and the Investment AI team
**Prototype:** `Evidence Check prototype.html` · **Presentation:** `Evidence Check Presentation.html`
All figures and documents in the prototype are illustrative demo data.

---

## Summary

**The product.** The Evidence Layer decides where to look for each figure in a committee memo and which figure to use, shows the committee the result with its proof, and keeps a replayable record of every step. Users see it as **Evidence Check**, inside (In)Alpha and Credit Companion.

**The problem.** Figures in a memo come from several sources that use different definitions (for example three EBITDAs, or two valuation multiples), and nobody can see how one was chosen. Analysts reconcile by hand, and a committee cannot challenge what it cannot trace.

**Who it is for.** Lead underwriters and investment analysts who prepare memos; committee members who read and challenge them; IC secretaries and auditors who submit and verify; the product team who improve the platform.

**Key features**
1. **One action to start.** Choose the memo type and the documents, then click Prepare memo or Generate IC memo.
2. **The system decides, and says why.** Every disputed figure comes with a decision in plain words and the effect of each alternative.
3. **You stay in control.** Accept the decision or change it with a reason, and save your own rule.
4. **Evidence behind every figure.** Click a figure to see its sources and the exact page.
5. **Where we looked.** See which sources were used or skipped and why, and set your own logic for choosing them.
6. **Audit trail.** A numbered record of every step for each figure, exportable for audit.

Also: grounded questions and answers in English and Arabic, and a product-team view of how the engine performs.

**What success looks like.** For the committee: any figure can be traced to its source page in two clicks, and no memo reaches committee with a figure nobody has reviewed. For analysts and underwriters: time goes to judgement, not to reconciling numbers. For the platform: every decision can be replayed, and the team can tell which sources are worth paying for. Section 7 gives the measures; baselines are taken in the first 30 days.

---

## 1. Product brief

## 2. Users

| Persona | Role | Needs |
|---|---|---|
| **Lead underwriter** (Credit Companion) | Prepares the credit memo and decides disputed figures | Settle disagreements fast and defensibly |
| **Investment analyst** ((In)Alpha) | Builds the IC memo and decides how to present figures | Accurate valuation numbers without manual reconciliation |
| **Committee member** | Reads the memo, may challenge | Trust a number, or challenge it, without reading source documents |
| **IC secretary / auditor** | Submits the memo and audits it | No unresolved figure at submission; replay any decision |
| **Product / applied science** | Improves the platform | See which sources add value, and at what cost |

---

## 3. The two use cases

### 3.1 Credit: "Is this borrower within its covenant?" (Credit Companion, Project Falcon)
- **Input:** the user uploads the documents (signed loan agreement, audited annual report, lender presentation) and clicks **Prepare memo** for a credit memo (new facility approval), with an optional focus. Context sent with the request: memo type, persona, workflow, client, constraints (private borrower, sovereign data), language.
- **What happens before the result:** the package is read page by page, drafts are set aside, each figure is pinned to entity, period, unit and definition; the router checks sources (vendors skipped: no coverage); figures are extracted with page references; the contract definition is read and applied; figures are compared and the gap explained ($8.5M share-based pay allowed, $4.5M lease adjustment not); the rule is applied; leverage and headroom are computed; everything is logged.
- **Verdict:** *pick one*. The loan agreement definition, $150.5M, leverage 3.11x, headroom 0.39x. **Decided by the system**; the underwriter reviews it, accepts it, or changes it with a reason.

### 3.2 Investment: "What multiple is the target trading at?" ((In)Alpha, Project Atlas)
- **Input:** the user chooses the target, confirms the request ("Analyze 5-year EBITDA trend and current valuation multiples relative to industry peers") and clicks **Generate IC memo** for an IC memo (proposed take-private), with the documents (the annual report), the language, an optional focus. The data sources are managed by (In)Alpha, not chosen by the user.
- **What happens before the result:** the request is split into history, live data and peers; the router plans each (stored filings copy, a low-cost FactSet batch, one live Bloomberg lookup; PitchBook and AlphaSense skipped); data is normalised (LTM versus forward basis); multiples are computed (12.4x and 12.8x); the gap is diagnosed as basis, not error; the rule is applied; alternatives are prepared with memo wording; everything is logged.
- **Verdict:** *label both*. "12.4x LTM · 12.8x NTM", never averaged. **Decided by the system** under policy; the analyst can review it, accept it or change it with a reason.

### 3.3 What a full memo contains
The demo shows only the key figures from two sections of each memo. The memo type selects a template listing the sections and the figures each one needs, and every number in the drafted text is also checked. Section lists below follow general practice and must be validated with a real credit officer and analyst.

| Credit memo | Investment (IC) memo |
|---|---|
| Request and purpose, Borrower overview, Industry | Executive summary, Company and thesis, Market and competition |
| **Financial analysis**, Facility structure, Security | **Financials**, **Valuation**, Transaction and returns |
| **Covenants and headroom**, Risk rating | Due diligence findings |
| Risks and mitigants, Recommendation | Risks and mitigants, ESG and compliance, Next steps |

Figures most likely to disagree: EBITDA (definition), net debt (leases, restricted cash), collateral values (book versus appraised), ratios (covenant versus vendor version), multiples and enterprise value (date and basis), forecasts (management versus consensus). Most figures agree across sources and need no decision.

---

## 4. Requirements

### 4.1 Functional requirements
| ID | Requirement |
|---|---|
| FR-1 | **Memo request.** The user starts one action (Prepare memo / Generate IC memo), picks a memo type, gives the deal parameters and an optional focus. The memo type selects a template with the required sections and figures. The request carries task, persona, workflow, client, constraints and language to the platform. |
| FR-2 | **Router.** For each figure, plan where to look: stored or borrower documents first, the lower-cost vendor next, a live lookup only when the figure must be live. Check vendor coverage and licences. Record every skip with its reason. Show the default rules with their reasons, record the cost and response time of every vendor call, and let a client override a default rule, with a reset to default. |
| FR-3 | **Extraction and normalisation.** Pin each figure to entity, period, unit, scope and metric definition. Identify the signed document version and set drafts aside. Keep page anchors. |
| FR-4 | **Resolver.** Detect differences (thresholds set per metric and materiality). Diagnose the cause: definition, basis, scope, timing or error. Return one of two verdicts: **pick one** or **label both**. Apply a stated trust order. Never average. Give a plain-language reason and preview the effect of each option. |
| FR-5 | **Human oversight.** The system always provides a decision with its reason. The user can review it, accept it, or change it with a required reason, and can save a client rule. Block submission until every system decision has been accepted or changed. Named sign-off by a person for specific figures is an optional client policy, off by default. |
| FR-6 | **Audit trail.** One numbered chain per figure: what was needed, where we looked and skipped, what we found (with page), the rule, the decision, who decided or reviewed it, and when. Include the request that started it. Link steps to the source page. Export as an audit pack. |
| FR-7 | **Source value.** For each source: used, reused or skipped, the figures it supplied or confirmed, whether it changed the answer, cost, response time and a verdict (keep or skip next time). Feed this into the routing rules. Cost and time come from usage metering and vendor contract rates. |
| FR-8 | **Grounded Q&A** in English and Arabic, with links to the source page, answering from the resolved figures. |
| FR-9 | **Autonomy policy.** The level of automation is configurable per client and workflow. The default is that the system decides and the user reviews. A client can require named sign-off for specific figures, and the platform recommends returning a figure type to sign-off when its override rate rises (for example above 25%). The audit trail records whether the system or a person decided. |
| FR-10 | **Platform insights (product team only).** Aggregate across memos, per workflow: for each source, how often it was used, supplied a figure and changed the answer, its cost per call and response time; for each resolution type, how often users accepted or overrode it and why; user ratings, comments and reported problems; and a backlog of proposed changes to the router and the resolution engine. Cost and response time are not shown in the client's view. In the first release, quality is judged from client feedback only (acceptance, overrides with reasons, ratings, comments, reported problems); precision and accuracy measured against ground truth come next. |

### 4.2 Non-functional requirements
- **Sovereign deployment:** documents, inference and decisions stay in-country; vendor access through an approved gateway inside the boundary.
- **Entitlements:** figure-level access follows the user's permissions and vendor licences.
- **Auditability:** records are immutable; retention period to be set with Compliance.
- **Performance:** a fast path for figures from stored or structured data and a deeper path for document reading; targets to be set after the baseline.

### 4.3 Out of scope for the MVP
Averaging vendors (never), live streaming market data, a model that changes routing automatically, memo authoring.

---

## 5. Experience

1. **Start screen.** The one user action: memo type, parameters, the documents, the language, an optional focus, and the list of memo sections. After the click, short progress steps show the work being done.
2. **Memo.** Plain paragraphs with each figure as a clickable chip (verified, decided by the system, decided by you). A banner counts key figures verified and flags system decisions awaiting review. Submit is disabled until each system decision is accepted or changed.
3. **Evidence panel.** Appears on click: for a verified figure, its sources and a link to the page; for a disputed figure, the system's decision, alternatives with their effect, the reason, the rule applied, change with a required reason, and "Remember this choice".
4. **Where we looked.** The router made visible, by source: the default rules and why, any client override, what each source contributed with its evidence and pages, what was skipped and why, whether each source changed the answer, and a verdict (keep or skip next time). Cost and response time are not shown here; they live in the platform team view.
5. **Audit trail.** By figure: the request that started it, then one numbered chain per figure, with export. Any figure can be reported as a problem, which logs a follow-up and shows on the memo; it does not block submission.
6. **Platform team view.** A separate screen for the product team, never shown to clients: router performance with cost and response time, how often users accept the system's resolutions, user feedback, and proposed improvements.

Design rule: a committee member must understand the memo screen in under 10 seconds. Detail lives in the other tabs.

---

## 6. User stories

| # | As a… | I want… | So that… | Acceptance |
|---|---|---|---|---|
| 1 | Lead underwriter or investment analyst | to start a memo with one action, choosing the documents and the language | I do not prepare each figure by hand | Memo type, documents, language and optional focus are on one screen; one click starts the run and shows progress; the memo opens with each figure marked |
| 2 | Committee member | to click any figure and see its source | I can trust it without reading the documents | Every figure opens a panel with its sources and a link to the page |
| 3 | Lead underwriter | a decision with a plain reason when sources disagree | I can review it in minutes | The decision, the reason, the rule applied, and the effect of each alternative on leverage |
| 4 | Lead underwriter | to choose a different figure and record why | the team understands the override | A reason is required; the memo, leverage and headroom update; logged with name and time |
| 5 | Investment analyst | two bases of a multiple shown together, labelled | the committee is not misled by one number | The decision is "show both, labelled"; no averaging; evidence for each basis |
| 6 | IC secretary | to be blocked from submitting until each system decision is accepted or changed | no unreviewed number reaches committee | Submit is disabled until every system decision has been accepted or changed |
| 7 | Auditor | to replay any figure from request to decision | I can verify the path | A numbered chain with source pages, the rule, and who decided; exportable |
| 8 | Product or applied-science team member | to see which sources were used, skipped, and whether they helped | we spend on sources that add value | Per source: used or skipped, why, the evidence, and whether it changed the answer |
| 9 | Credit officer | to ask in Arabic and get an answer with sources | I can work in my language | An Arabic answer with clickable source links |
| 10 | Lead underwriter | to save my choice as a client rule | I am not asked the same question again | "Remember" creates a visible rule that I can change or reset |
| 11 | Investment analyst | the system to decide every disputed figure, and to review and change it | I spend my time on judgement, not reconciliation | The system's decision is shown with its reason; I accept it or change it with a reason |
| 12 | Lead underwriter | to define my own logic for choosing sources | the platform follows my client's policy | Rules can be edited, switched on or off, reordered, added, and reset to the default |
| 13 | Any user | to report a problem with a figure | someone follows it up | A note is required; a follow-up is logged and shown on the memo; it does not block submission |
| 14 | Any user | to say whether an explanation was helpful | the product team can improve it | Yes or No with an optional comment; logged and visible in the platform team view |

### Interaction journeys

**1. Lead underwriter, credit case (Credit Companion).** *Goal: get a credit memo whose covenant figures she can defend.*
1. Opens "Prepare credit memo" for Falcon and checks the memo type, the three documents and the language.
2. Clicks **Prepare memo** and watches the progress steps.
3. Opens the memo: four figures are green, Adjusted EBITDA is blue (decided by the system).
4. Clicks it and reads the decision: $150.5M, the loan agreement's definition, with leverage of 3.11x against 3.30x and 3.02x for the alternatives.
5. Opens the evidence: the definition on page 31 of the loan agreement.
6. Accepts the decision, ticks "Remember this choice", and submits.

**2. Investment analyst, investment case ((In)Alpha).** *Goal: present valuation against peers without misleading the committee.*
1. Chooses Atlas, confirms the request, picks the memo type and clicks **Generate IC memo**.
2. The memo shows the multiple as "12.4x LTM · 12.8x NTM", decided by the system.
3. Opens the evidence for both bases: the annual report (LTM) and the Bloomberg snapshot (forward).
4. Prefers an LTM headline, so uses **Change decision**, picks "LTM reported only" and writes why. The memo updates.

**3. Committee member.** *Goal: trust a number, or challenge it, in the meeting.*
1. Reads the memo and clicks a figure to see its sources.
2. Asks in Arabic why the EBITDA differs and gets an answer with source links.
3. Notices a link opening the wrong page, uses **Report a problem**, and a follow-up appears on the memo.

**4. IC secretary or auditor.** *Goal: verify the path behind every figure.*
1. Opens the Audit trail and reads what started the memo.
2. Expands the EBITDA figure: needed, where the system looked and skipped, what it found, the rule, the decision, and who reviewed it.
3. Opens a source page from a step and exports the audit pack.

**5. Product team member.** *Goal: decide what to improve in the router and the resolver.*
1. Switches to the Platform team view.
2. Reads, per source, how often it changed the answer and what it costs, and, per resolution type, how often users overrode it and why.
3. Reads the user ratings and reported problems, then picks a backlog item, for example "call Bloomberg only when a live price is needed".

---

## 7. Success metrics
Hypotheses; baselines are measured in the first 30 days before any target is committed.

| Metric | Target |
|---|---|
| Figures in a memo with a source link | 100% |
| Unresolved conflicts at submission | 0 |
| Time to reconcile figures per memo | Baseline first, then agree a target |
| Override rate and top override reasons | Tracked; feeds rule review |
| Paid lookups avoided with no change in outcome | Tracked per workflow |
| Decisions replayable from the audit trail | 100% |

---

## 8. Risks and open questions
- **Licensing:** caching vendor data and showing vendor figures to a user may be restricted by contract and by that user's entitlements.
- **Coverage:** vendors may not cover private borrowers; the router must say so, not fail silently.
- **Entity and metric mapping** across vendors and documents is the main source of silent errors.
- **Definition extraction:** reading a contract definition automatically needs accuracy testing; a human confirms until proven.
- **Sovereign boundary versus live vendor feeds** needs a gateway design agreed with Engineering.
- **Thresholds:** one flat variance threshold is wrong; set per metric and materiality.
- **Who may adjudicate:** maker/checker rules above a materiality limit.
- **Automation bias:** people may accept system decisions without checking. Keep the reason prominent, require a reason only when overriding, sample-audit system decisions, and widen autonomy only as evidence supports it.
- **Validation:** the memo sections, use cases and trust order follow general practice and must be tested with real users.

---

## 9. Commodity versus G42 IP
| Commodity (reuse) | Defensible G42 IP (build) |
|---|---|
| Citations, document chat, semantic search | A router that explains what it skipped and why |
| Basic extraction from filings | Resolution with two verdict types (pick one, label both) |
| | Per-client rules learned from committee decisions |
| | Source-value measurement: did this lookup help, at what cost? |
| | Sovereign deployment and Arabic-native workflows |

---

## 10. Roadmap: first 90 days
- **Days 0–30:** map products and data needs, build the source inventory and competitor view, measure today's reconciliation baseline, validate the two use cases with a credit officer and an analyst.
- **Days 31–60:** source-by-workflow matrix (the router's rules), MVP scope, source-value evaluation criteria, build, buy and partner decisions.
- **Days 61–90:** one engine live in two products: the investment case in (In)Alpha and the credit case in Credit Companion, each with its audit trail.

**Asks:** two or three anonymised real memos with known figure disputes; an Applied Science partner for the evaluation set; a Legal view on displaying and caching vendor data.

---

## Appendix: glossary
- **Router:** decides where to look for each figure and records what it skipped.
- **Resolver:** decides which figure to use and why when sources disagree.
- **Evidence Layer:** the capability as a whole: source router, resolver and audit trail.
- **Evidence Check:** the screen the user sees inside (In)Alpha and Credit Companion.
- **Pick one / label both:** the two verdict types. Pick one chooses a single figure; label both shows each figure with its basis.
- **Audit trail:** the numbered, replayable record of every step behind a figure.
- **LTM / NTM:** last twelve months / next twelve months.
