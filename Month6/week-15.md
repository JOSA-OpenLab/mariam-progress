# ★ Pull Request // Implement a Queue Lab

* **Issue :** [#70332](https://github.com/freeCodeCamp/freeCodeCamp/issues/70332)
* **The Scenario :** The tests for the *Implement a Queue* lab (JS v9 certification) had hidden dependencies between learner-written functions, so learners couldn't pass one function's tests without implementing others first. An earlier PR [(#70492)](https://github.com/freeCodeCamp/freeCodeCamp/pull/70492) had already moved most assertions onto `queue.collection`, but it stalled after the maintainer flagged that the `enqueue`, `size`, and `isEmpty` tests still called `dequeue`, an undisclosed dependency.
* **My PR :** [PR #70594](https://github.com/freeCodeCamp/freeCodeCamp/pull/70594)
  I built on that work and replaced the remaining `dequeue` calls in those three tests with a direct operation on the queue's array:

```js
// before
dequeue(queue);

// after
queue.collection.shift();
```
  This leaves `enqueue` as the only dependency, which I stated in the instructions the same way the *Implement a Stack* lab does.
* **Verification :** Ran `FCC_BLOCK='lab-implement-a-queue' pnpm run test-curriculum-content`, then checked that each function's tests pass when only `enqueue` and that function are implemented.

> *Testing each function in isolation was the step that caught what the first attempt missed.*

# ★ PR Comment // Coordinating with the Original PR

* **PR Commented On :** [PR #70492](https://github.com/freeCodeCamp/freeCodeCamp/pull/70492)
* **My Comment :** [Comment](https://github.com/freeCodeCamp/freeCodeCamp/pull/70492#discussion_r4173433373)
  Linked my PR, explained the `queue.collection.shift()` fix, and offered to close mine if the fix should land on the original branch instead.
* **Outcome :** The original author applied the same change in commit `f75b9aa`, so both branches now have the same file content.
* **Next Step :** #70594 is labeled `status: waiting review`. I'm waiting for the maintainer to decide which PR moves forward before closing either one.

> *Opening a competing PR without saying anything would have split the review and risk closing the PR as douplicate. Commenting on the original kept the conversation in one place.*

# ★ Pull Request // Movie Ticket Booking Calculator Step 21

* **Issue :** [#70696](https://github.com/freeCodeCamp/freeCodeCamp/issues/70696)
* **The Scenario :** Step 21 of the *Movie Ticket Booking Calculator* workshop (Python v9 certification) asks learners to subtract a discount to get the final ticket price. It's the first binary subtraction task in the Python Basics workshops, but unlike the earlier steps for addition, multiplication, and division, it gave no syntax reminder or example for the `-` operator.
* **My PR :** [PR #70704](https://github.com/freeCodeCamp/freeCodeCamp/pull/70704)
  I added a short explanation of the subtraction operator and an example before the task instructions:

```py
amount_due = total_cost + shipping - coupon
print(amount_due)  # 45
```
  The example combines `+` and `-` in one expression, like the task requires, but uses unrelated variable names so it doesn't give away the solution. Only the description changed; hints, seed code, and solution are untouched.
* **Verification :** Ran the curriculum tests for `workshop-movie-ticket-booking-calculator`, and all `106/106` passed. On Windows, this took setting pnpm's script shell to Git Bash and running turbo with `--env-mode=loose`, because Command Prompt can't parse the scripts' inline `NODE_OPTIONS` variables.

> *The fix took minutes; getting the tests to run on Windows took most of the effort.*
