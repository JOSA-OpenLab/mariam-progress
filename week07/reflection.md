# ★  Add Dependabot // TASK 1
Configured Dependabot with both code and github-actions ecosystems. Pined all Actions to commit SHAs and submited it as a PR.  
* [PR link](https://github.com/mariamanbar/bast-space/pull/3)

# ★ Sign a release // TASK 2
Since this repository does not use automated GitHub Action workflows, I handled the release packaging manually from my local terminal instead. I first bundled my core TouchDesigner assets into a clean archive (`release.tar.gz`), using filter flags to strip out local `Backup` and `site` directories so the final artifact stays lightweight.  
Next, I downloaded `cosign` and initiated a keyless identity verification flow through my browser, authenticating with my GitHub profile to log a cryptographic signature on the public Rekor transparency log. Finally, I updated my project's `README.md` with the exact `cosign verify-blob` command so anyone downloading the package can verify its authenticity and confirm it hasn't been tampered with before running the installation.

# ★  Generate and read an SBOM // TASK 3
1. Used Anchore’s Syft and compiled a Software Bill of Materials (SBOM) in the CycloneDX JSON format from the root directory of the freeCodeCamp repository (local clone).
   * **Packages Cataloged:** 2,878
   * **packages  Executables Cataloged:** 106
   * **executables  Ecosystem:** Node.js (pnpm workspace)
   * **Observations:** Running an automated SBOM tool over a repo of this scale immediately exposes how massive the modern open-source dependency tree is. While our active contributions are scoped to specific curriculum files, the local environment inherits nearly 3,000 transient packages through node_modules. This perfectly maps to the software supply chain attack: every nested third-party utility represents an external point of trust and a potential vulnerability vector.

2. used Grype to run a vulnerability scan targeting the newly created static SBOM file:
   * Total Vulnerabilities Found: 151 matches (7 Critical, 54 High, 65 Medium, 25 Low)
   * Fixability: 149 of the vulnerabilities have available upstream fixes, 2 not-fixed
3. CVE Investigation:  
   I selected the first High severity vulnerability from the output for a closer look:
   * **Package Name:** `vite`
   * **Installed Version:** `7.3.1` (Fixed in: `7.3.2`)
   * **Vulnerability ID:** `GHSA-p9ff-h696-f583` / `CVE-2024-52599` (osv.dev) 
   * **Severity:** High (Risk Score: 2.3)
  
   What is it?  
   * This vulnerability allows for an Arbitrary File Read via the Vite development server's WebSocket connections. Normally, Vite's dev server implements filesystem security checks (`server.fs.allow`) to block unauthorized HTTP requests trying to escape the project directory and read random local files (like `/etc/passwd` or system env files). However, a flaw was discovered where this restriction was completely bypassed when interacting with Vite's Hot Module Replacement (HMR) WebSocket channel.
   * If an attacker connects to the Vite dev server's WebSocket connection without an `Origin` header, they can invoke an internal custom event (`vite:invoke`) to run a method called `fetchModule`. By passing a local file URL scheme (e.g., `file://...`) appended with `?raw`, the server skips file tracking controls and returns the full contents of arbitrary machine files wrapped as a JavaScript string module.  
   
   Who Is Affected?
   Developers or environments are only actively vulnerable if they fulfill both of these conditions
   * They are running a vulnerable version of Vite (6.x < 6.4.2, 7.x < 7.3.2, or 8.x < 8.0.5).
   * They explicitly expose the Vite dev server to the external network using the `--host` flag or `server.host: true` configuration option.
  
   *Note: Production application builds are not affected by this, as it exclusively plagues the active local development server.*

   What Is the Fix?
   * **The Fix:** Upgrade the project's `vite` dependency to a patched version (at least `7.3.2` as indicated by Grype, or `6.4.2` / `8.0.5` depending on the major version branch).
   * **Immediate Mitigation:** Avoid running `vite --host 0.0.0.0` in untrusted or public network environments. Leave the dev server locked down to localhost (`127.0.0.1`) so external actors cannot probe the open port.

# ★ Run Scorecard on a project // TASK 4
Ran an OpenSSF Scorecard assessment on the `freeCodeCamp/freeCodeCamp` repository and identified a `0/10 risk` rating on `workflow token permissions`. To mitigate this risk, I opened a Pull Request to enforce the principle of `least privilege` by adding strict, explicit `permissions:` blocks to three legacy background utility workflows.
