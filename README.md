# mega-dev-s01e03

MEGA.dev challenge S01E03 ("what it shows"). Support ticket #4471 merged 412 date reports into one issue. It holds two problems.

- **Issues** are the tickets, one per problem (issue 1: 379 reports, issue 2: 33), each with its report count. Issue 3 was folded into issue 1.
- **Page** `src/routes/kb/incidents/4471-split/+page.svx` is the kb page about them. It uses the new component `src/lib/kb/ProblemTable.svelte`.
- **`page.html`** is the page as the desk sees it, rendered from the same data.
- `src/` and `KB-README.md` are the support desk's kb export, unchanged except for the two new files above.
