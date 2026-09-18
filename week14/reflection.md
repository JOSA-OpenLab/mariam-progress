### ★ Week 13 : Practical Platform Engineering & Test Hardening
* **Target Module:** `api/src/routes/protected/settings.test.ts` and `api/src/routes/protected/settings.ts`.
* **Issue Discovered & Reported:** Identified a test-coverage gap in the API's protected settings routes: an existing `Unauthenticated User` regression test parametrizes 401 checks across all protected PUT routes, but silently omitted three of them (Tracked under [issue #70182](https://github.com/freeCodeCamp/freeCodeCamp/issues/70182)).
* **Key Technical Gaps Found:**
  * **Missing Coverage (`/update-my-email`):** Route has its own full `describe` block with 9+ tests, but was never added to the `endpoints` array that verifies unauthenticated access is rejected.
  * **Missing Coverage (`/update-socrates`):** Same gap — fully tested for authenticated behavior, untested for the unauthenticated case.
  * **Missing Coverage (`/update-my-classroom-mode`):** Same gap, notable because this route also enforces a one-way constraint (cannot be disabled once set), making it the highest-value route to have a solid regression net around.
* **Verification of Non-Vulnerability:** Before filing, traced `api/src/app.ts`'s route registration to confirm all three routes sit inside the same `fastify.register` block applying the `send401IfNoUser` / `authorize` / CSRF hooks as every already-tested route — ruled out an active vulnerability before treating this as a test-only issue.
* **Remediation & Verification:**
  * Added the three missing route entries to the existing `endpoints` parametrized test array.
  * Verified the diff against `git show --stat` (4 insertions, 1 deletion — matching expected scope exactly) before pushing.
  * Relied on CI for the full integration-test run rather than local Docker/Mongo setup.
* **Next Action:** Pushed branch `test/settings-401-coverage` and opened the [pull request](https://github.com/freeCodeCamp/freeCodeCamp/pull/70183) to merge the regression-coverage fix into `freeCodeCamp/freeCodeCamp`.
