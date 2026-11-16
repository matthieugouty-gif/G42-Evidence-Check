# Mini PRD: Evidence Check

**Status:** Draft for discussion · **Author:** Matthieu Gouty · **Audience:** Robin, Mohammed Shayan and the Investment AI team
All figures in the prototype are illustrative demo data. Full version: `Evidence Check PRD.md`.

---

## 1. Summary

**The product.** The Evidence Layer decides where to look for each figure in a committee memo and which figure to use, shows the committee the result with its proof, and keeps a replayable record of every step. Users see it as **Evidence Check**, inside (In)Alpha and Credit Companion.

**The problem.** Figures in a memo come from several sources that use different definitions (three EBITDAs, two valuation multiples), and nobody can see how one was chosen. Analysts reconcile by hand, and a committee cannot challenge what it cannot trace.

**Who it is for.** Lead underwriters and investment analysts (prepare memos), committee members (read and challenge), IC secretaries and auditors (submit and verify), the product team (improve the platform).

**Success.** Any figure traces to its source page in two clicks; no memo reaches committee with an unreviewed figure; every decision can be replayed; the team can tell which sources are worth paying for.

---

## 2. The two use cases

**Credit: "Is this borrower within its covenant?" (Credit Companion, Project Falcon).** The user uploads the loan agreement, annual report and lender presentation and clicks **Prepare memo**. The system reads the package, compares the EBITDA figures and explains the gap. Verdict: *pick one*, the loan agreement definition, $150.5M, leverage 3.11x, headroom 0.39x. The underwriter accepts it or changes it with a reason.

**Investment: "What multiple is the target trading at?" ((In)Alpha, Project Atlas).** The user confirms the request and clicks **Generate IC memo**. The system plans history, live data and peers, and diagnoses the gap as basis, not error. Verdict: *label both*, "12.4x LTM · 12.8x NTM", never averaged. The analyst accepts it or changes it with a reason.

---

## 3. Core capabilities

1. **Memo request:** one action; memo type selects a template of required sections and figures.
2. **Router:** plans where to look per figure (stored documents first, lower-cost vendor next, live lookup only when needed), checks coverage and licences, records every skip with its reason, and lets a client override the default rules.
3. **Resolver:** detects differences, diagnoses the cause (definition, basis, scope, timing, error), returns *pick one* or *label both*, never averages, and explains the effect of each option.
4. **Human oversight:** the system always decides and gives its reason; the user accepts or changes it with a reason, and can save a client rule. Submission is blocked until every system decision is reviewed.
5. **Audit trail:** one numbered, exportable chain per figure, linked to the source page.
6. **Grounded Q&A** in English and Arabic, with source links.
7. **Source value and platform view (product team only):** per source, whether it was used, changed the answer, and what it cost, feeding back into the routing rules.

**Non-functional:** sovereign deployment (documents, inference and decisions stay in-country); figure-level entitlements follow user permissions and vendor licences; immutable audit records.
**Out of scope for the MVP:** averaging vendors (never), live streaming data, automatic routing changes, memo authoring.

---

## 4. Why build it

| Commodity (reuse) | Defensible G42 IP (build) |
|---|---|
| Citations, document chat, semantic search | A router that explains what it skipped and why |
| Basic extraction from filings | Resolution with two verdict types (pick one, label both) |
| | Per-client rules learned from committee decisions |
| | Source-value measurement: did this lookup help, at what cost? |
| | Sovereign deployment and Arabic-native workflows |

---

## 5. Success measures
Baselines are measured in the first 30 days before any target is committed.

| Metric | Target |
|---|---|
| Figures in a memo with a source link | 100% |
| Unresolved conflicts at submission | 0 |
| Decisions replayable from the audit trail | 100% |
| Override rate and paid lookups avoided | Tracked; feeds rule review |

---

## 6. Top risks
- **Licensing:** caching and displaying vendor data may be restricted by contract and entitlements.
- **Coverage:** vendors may not cover private borrowers; the router must say so, not fail silently.
- **Entity and metric mapping** across vendors and documents is the main source of silent errors.
- **Sovereign boundary versus live vendor feeds** needs a gateway design agreed with Engineering.
- **Automation bias:** people may accept system decisions unchecked; keep the reason prominent, sample-audit decisions, widen autonomy only as evidence supports it.

---

## 7. First 90 days and asks
- **Days 0–30:** source inventory, reconciliation baseline, validate the two use cases with a credit officer and an analyst.
- **Days 31–60:** source-by-workflow matrix (the router's rules), MVP scope, build/buy/partner decisions.
- **Days 61–90:** one engine live in two products, each with its audit trail.

**Asks:** two or three anonymised real memos with known figure disputes; an Applied Science partner for the evaluation set; a Legal view on displaying and caching vendor data.
