# ★ Security Research & Vulnerability Scoping // Weeks 11 & 12

## ★ 1. Research Overview & Objective
* **Repo:** [freeCodeCamp/freeCodeCamp](https://github.com/freeCodeCamp/freeCodeCamp)
* **Objective:** Conduct a security investigation across the freeCodeCamp monorepo to identify high-impact, technically deep contributions rather than superficial documentation edits or low-effort dependency version bumps.

## ★ 2. Two-Week Investigation Log

### Week 11: Tooling Audit, Threat Modeling & Surface Scans
* **Disclosure & Bot Infrastructure:** Confirmed that freeCodeCamp requires private email disclosure (`security@freecodecamp.org`) for actual vulnerabilities. Checked their Renovate bot setup, they maintain a live Dependency Dashboard [issue #65122](https://github.com/freeCodeCamp/freeCodeCamp/issues/65122) that automatically opens PRs for routine dependency bumps, so generic version updates would just get closed as duplicates.
* **Studied past security fixes:** Looked through closed advisory [GHSA-qf62-hv7h-v8m8](https://github.com/freeCodeCamp/freeCodeCamp/security/advisories/GHSA-qf62-hv7h-v8m8) (JWT storage in browser). Reviewed how they shifted auth tokens from localStorage/sessionStorage over to `HttpOnly` cookies to understand their client-side auth model.
* **Ran a full monorepo scan with Grype:** Generated an SBOM and scanned it with `grype sbom.json`:
* **Monorepo Vulnerability Scanning (`grype`):** Generated an SBOM for the monorepo and executed a vulnerability scan (`grype sbom.json`):
  * **Matches:** 206 total matches (7 critical, 96 high, 75 medium, 28 low).
  * **Status:** 204 matches already possessed upstream patches queued or handled by Renovate.
  * **Zero-Fix Candidates:** Isolated the only 2 vulnerabilities lacking published upstream fixes: `elliptic` (`GHSA-848j-6mx2-7j84`) and `dompurify` (`GHSA-x4vx-rjvf-j5p4`).

### Week 12: Codebase Tracing & Reachability Analysis
* **Cloned the repo and traced `pnpm-lock.yaml`:** Mapped out how both unfixed dependencies actually enter the build to see if the vulnerable methods are ever called.
  * **`dompurify` (v3.2.7):** Comes in via `monaco-editor`. Traced Monaco's usage in the challenge UI—it only calls DOMPurify's standard string sanitizer for markdown rendering, never the buggy `IN_PLACE` live-DOM mode required for the bypass. **Not reachable.**
  * **`elliptic` (v6.6.1):** Comes in transitively via `crypto-browserify` inside Gatsby's Webpack config (`client/gatsby-node.ts`). Checked the codebase call sites; freeCodeCamp only calls `randomBytes`, `createHash`, and `randomUUID`. The vulnerable ECDSA signing functions are never imported or executed. **Not reachable.**
* **Checked remaining critical findings:** Looked at transitive packages like `form-data`, `sanitize-html`, and `shell-quote` to verify if any were actionable at runtime; all were either build-time-only tools or already managed by package overrides.

## ★ 3. Key Findings & Technical Verdict
* **Static Scanners vs. True Reachability:** Demonstrated that automated SBOM vulnerability scanners produce high rates of false positives when evaluated without call-graph reachability. Both zero-fix vulnerabilities are inert within freeCodeCamp's runtime context.
* **Maintainer Bandwidth & Contribution Hygiene:** In-depth source auditing prevented submitting redundant or non-viable PRs, adhering to the project's contributing standards and saving maintainer review bandwidth.

## ★ 4. Contribution Strategy & Next Steps
| Track | Focus & Action Plan |
| :--- | :--- |
| **Security Track (Private)** | Continue auditing client-side route transitions and authentication endpoints for token-handling regressions against the established `HttpOnly` cookie model & other valuable security points. |
| **Platform Engineering Track (Public)** | Transition investigation findings into direct code fixes on verified platform issues, prioritizing `platform: learn`, `platform: api`, and `scope: a11y` modules over simple curriculum text updates. |
