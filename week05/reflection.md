# ★ Add tests where there are none // TASK 1
* **Problem :** The challenge asks developers to compute a snowboarder's landing stance given a starting stance and a rotation. However, the current test cases only validate variations using the `"regular"` starting stance.
  this creates a false-positive scenario where an incorrect implementation can completely ignore the `startStance` parameter and still pass every single test.
* [PR Link](https://github.com/freeCodeCamp/freeCodeCamp/pull/67968)

# ★  Build a real workflow // TASK 2
* Added a CI pipeline to my project with a .github/workflows/ci.yml file. It's set up to check my code across multiple versions of Node.js (v20 and v22), use dependency caching to speed things up, and run my automated test suite.  
* Added a status badge right at the top of my README.md file.  
* [Repository Link](https://github.com/mariamanbar/bast-space)

# ★ Run with `act`
What I noticed:
* Platform Limits: The local `act` tool automatically skipped the Windows tests (windows-latest) and only ran the Ubuntu ones. This happens because local act relies on a Linux-based Docker setup. GitHub's cloud servers, however, can spin up real Windows environments automatically.
* Hidden Workflow Files: The repository contained a default GitHub Pages deployment file that ran automatically when `act` is used. It failed because it couldn't find the secure cloud keys (tokens) it needs to talk to GitHub's servers, so I used `act -j test` to bypass it and only run my specific test suite.
