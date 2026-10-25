# ★ Vulnerability Report & Fix // C# Exam Score Inflation via Duplicate Question Submissions

- **PR Opened by Maintainers :** [#71187](https://github.com/freeCodeCamp/freeCodeCamp/pull/71187)
- **The Scenario :** While auditing the API layer, I found a bug in `POST /exam-challenge-completed` affecting the *Foundational C# with Microsoft* exam certification. The schema validated that the count of submitted answers matched the total question count, but failed to verify that the question IDs were unique. Because `createExamResults` calculated scores per answer entry independently, a learner could submit the same correct answer multiple times (e.g., repeating 1 correct answer 5 times for a 5-question exam) to achieve a 100% score and claim the certification without completing the full exam.
- **My Findings & Report :** Disclosed privately to `security@freecodecamp.org` with code pointers across `api/src/utils/exam-schemas.ts`, `api/src/utils/exam.ts`, `api/src/routes/protected/challenge.ts`, and `api/src/routes/protected/certificate.ts`. Included a full reproduction setup, local `vitest` unit tests demonstrating the exploit payload, and suggested adding `.unique('id')` to `userExamQuestions` in `userCompletedExamSchema`.
- **Outcome :** The security team acknowledged the report and classified it as a functional API bug since it involved individual account score manipulation. Maintainers opened PR [#71187](https://github.com/freeCodeCamp/freeCodeCamp/pull/71187) implementing the `.unique('id')` schema restriction along with an added check for question ID validity, crediting my handle as the reporter.
- **Verification :** Authored and executed local unit tests in `vitest` comparing execution before and after the schema modification:

```js
// 1 of 3 correct
validation passes | score: 33.3% | passed: false

// 3 of 3 correct (valid submission)
validation passes | score: 100%  | passed: true

// Same correct question repeated 3 times (exploit payload)
// Before fix : validation passes | score: 100%  | passed: true
// After fix  : validation fails with 400 Bad Request (duplicate question IDs rejected)

Auditing the schema validation against array edge cases caught an issue that basic length-checking missed, allowing the team to patch the certification API safely before public disclosure.
