# ★ PR Reviews // TASK 1 
First PR: 
* **PR Reviewed :** [fix(curriculum): rounding edge case in challenge 312 #68101](https://github.com/freeCodeCamp/freeCodeCamp/pull/68101#issue-4701213084)
* **The Scenario :** A contributor attempted to fix a complex IEEE-754 floating-point rounding bug in a daily coding challenge by manually shifting the test inputs.
* **My Comment :** [Comment](https://github.com/freeCodeCamp/freeCodeCamp/pull/68101#issuecomment-4755455556), used a suggestion tag to nudge them toward an
  ongoing architectural discussion on the issue tracker. Instead of letting them rewrite test cases unnecessarily, I pointed them toward the maintainer's
  feedback about fixing the underlying reference solution's rounding logic directly.
    
  This Code review is about aligning a contributor’s enthusiasm with the architectural vision of the maintainers.
    
Second PR: 
* **PR Reviewed :** [fix(client): don't duplicate block name #68057](https://github.com/freeCodeCamp/freeCodeCamp/pull/68057#issue-4691942373)  
* **The Scenario :** A contributor was trying to fix a bug where page titles duplicated text (e.g., "Quiz - Quiz"). They got caught in a scope loop after a maintainer
  initially told them a file didn't need changes, only to realize later that multiple challenge templates (classic, generic, quiz) might actually be broken.  
* **My Comment :** [Comment](https://github.com/freeCodeCamp/freeCodeCamp/pull/68057#issuecomment-4755543012), I used historical codebase context to help the contributor make a design decision.
  By digging into a recently merged timeline PR [(#67912)](https://github.com/freeCodeCamp/freeCodeCamp/pull/67912), I found that the team had already established a global architectural
  norm: using a generic `blockNameTitle !== title` equality check instead of hardcoding narrow. I brought this context into my review with a suggestion tag, encouraging the author to
  scale their fix across the other affected templates `classic/show.tsx` and `generic/show.tsx` using that same unified approach.
