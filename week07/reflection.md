# ★  Add Dependabot // TASK 1
Configured Dependabot with both code and github-actions ecosystems. Pined all Actions to commit SHAs and submited it as a PR.  
* [PR link](https://github.com/mariamanbar/bast-space/pull/3)

# ★ Sign a release // TASK 2
Since this repository does not use automated GitHub Action workflows, I handled the release packaging manually from my local terminal instead. I first bundled my core TouchDesigner assets into a clean archive (`release.tar.gz`), using filter flags to strip out local `Backup` and `site` directories so the final artifact stays lightweight.  
Next, I downloaded `cosign` and initiated a keyless identity verification flow through my browser, authenticating with my GitHub profile to log a cryptographic signature on the public Rekor transparency log. Finally, I updated my project's `README.md` with the exact `cosign verify-blob` command so anyone downloading the package can verify its authenticity and confirm it hasn't been tampered with before running the installation.
