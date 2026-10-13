### ★ Week 13 : Practical Platform Engineering & Test Hardening
* **Target Module:** `api/src/plugins/csrf.ts` and `api/src/plugins/csrf.test.ts`.
* **Issue Discovered & Reported:** Identified and documented test false-positives and a routing edge case in the API's CSRF plugin (Tracked under [issue #69902](https://github.com/freeCodeCamp/freeCodeCamp/issues/69902)).
* **Key Technical Gaps Found:**
  * **Test False Positive (Invalid Token):** The test sent the bad token as a cookie instead of the `csrf-token` header, causing a 403 due to a missing header rather than actual cryptographic validation rejection.
  * **Hidden Failure (New Token):** The initial token test lacked a status code assertion and passed despite returning a 403 because no `_csrf` secret was sent.
  * **Routing Edge Case (/signout):** The exemption check relied on `req.url === '/signout'`, which broke when query strings were attached (e.g., `/signout?redirect=true`), causing unnecessary cookie reissuance.
* **Remediation & Verification:**
  * Switched the signout check to use Fastify's stable `req.routeOptions?.url`.
  * Corrected test payloads to explicitly pass headers and added proper status assertions.
  * Verified local stability using `pnpm dlx vitest run src/plugins/csrf.test.ts` (all 5 tests green).
* **Next Action:** Pushed branch `fix/api-csrf-tests` and prepared the [pull request](https://github.com/freeCodeCamp/freeCodeCamp/pull/69903) to merge platform improvements directly into `freeCodeCamp/freeCodeCamp`.
