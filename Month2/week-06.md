# ★ One real docs PR // TASK 1
* [PR Link](https://github.com/mdn/content/pull/44720)
* The current description on the Document: selectionchange event page is very brief and does not explain what counts as a change to the document selection.
The page also notes that this event differs from the events fired when the text selection in an or <textarea> changes. However, it does not explain what the actual difference is.

# ★ Set up a docs site // TASK 2
* [Docs Site](https://mariamanbar.github.io/touchdesigner-audio-reactive-mesh/)
* Added MkDocs Material, to my Audio Reactive Repo, deployed it via GitHub Pages, and linked it from the README.

# ★  Write the first ADR // TASK 3
* [ADR Link](https://github.com/mariamanbar/touchdesigner-audio-reactive-mesh/commit/362400b7bce483c93547eadc13fbbec8fbda1ee6)
* Documnted my decision about making a simple audio analysis network instead of using TouchDesigner's pre-built `audioAnalysis` palette component in the ADR file.

# ★   Add Vale to one repo // TASK 4
* [Commit Link](https://github.com/mariamanbar/bast-space/commit/541d292c81fc5f32f5c13f6b4fd350a895328225)
* Configured Vale and added it to the same CI built in Week 5. Since I did the documentation for a different repo, Vale is running just on the README.md for now.  
* The linter was complaining about all the passive voice in the README, so I swapped them for active verbs to keep it happy (I hate active verbs).
