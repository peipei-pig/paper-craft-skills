---
name: paper-to-hexo
description: Turn a research paper or an existing paper-explainer HTML into a reviewed standalone article and integrate it into a Hexo site, keeping the paper workspace and publishing copy synchronized. Use for the end-to-end paper-to-blog workflow or subsequent updates to an article managed by it.
---

# Paper to Hexo

Help the user move from a paper PDF or paper directory to a reviewed, locally previewable standalone HTML article in Hexo. Support continuing an existing article as well as creating one. Respect the user's explicit paths, style choices, scope, and publishing instructions.

## Related skills

- For paper analysis and HTML creation, read `../paper-analyzer/SKILL.md` or the installed `paper-analyzer` skill. Apply its relevant source-analysis and writing guidance; the user's requested scope, preferred style, and permissions take precedence over rigid quotas, forced prompts or steps that do not apply.
- For substantive review, read `../paper-html-reviewer/SKILL.md` or the installed `paper-html-reviewer` skill. Check the source paper, conceptual fidelity, figures, clarity and rendering. An independent review is not a requirement to spawn an agent.
- Resolve these paths relative to this skill when the repository is present; otherwise locate the installed skills. If a related skill is unavailable, say so and continue with the available guidance when the task remains feasible. Do not silently claim to have used it.

## Style defaults for the user's Hexo paper articles

When working with the user's existing Hexo paper site, use `D:/ml/hexo/source/papers/sglang/index.html` as the visual reference if it exists, unless the user requests a different design or asks to preserve an existing one. Match its light blue header and pale page background, readable contrast, and left-side sticky table of contents; on narrow screens, place the contents above the article as in the reference. Inspect the reference rather than assuming its current CSS values. Do not add a second inline navigation row that duplicates the table of contents. Check that every contents link has a matching heading, including the references section, and inspect desktop and narrow rendering.

Keep the opening compact: retain the paper and repository links where useful, but omit an author/affiliation/year line, code-publication announcements, displayed commit IDs, figure-number explanations, and PDF page-offset notes unless the user asks for them or they are essential to understanding a claim. Keep commit-pinned links for code excerpts when applicable. Do not add a process or provenance footer describing review steps, unrun experiments, or CDN/offline behavior by default; report material limitations to the user outside the article when appropriate, and retain qualifications necessary to prevent a misleading technical claim.

End with a concise **参考文献** section containing the analyzed paper and relevant sources actually used in the article, with accurate bibliographic details and links to authoritative sources. Do not invent references or imply an unrelated source was cited. User instructions and established article-specific choices override these defaults.

## Locations and source of truth

Use the paths supplied by the user. In the user's existing setup, `D:/ml/paper_read/<paper>/` (WSL: `/mnt/d/ml/paper_read/<paper>/`) is the paper workspace and `D:/ml/hexo/` (WSL: `/mnt/d/ml/hexo/`) is the Hexo site; treat these as defaults only if they exist and the user has not chosen other locations. Resolve Windows and WSL paths to the same underlying files. Inspect repository instructions, the real site configuration, and existing changes before editing.

The paper workspace owns the **canonical editable HTML and its article-specific assets**. The Hexo `source/` copy is a publication copy; Hexo `public/` is generated output and must never be treated as the source. Make future editorial changes to the canonical HTML first, then synchronize them to Hexo and rebuild. Keep the original PDF in the paper workspace unless the user requests otherwise; use an available official paper link rather than copying the PDF into the site by default. Verify the selected paper version against claims and references; do not assume an unversioned PDF URL always resolves to that version.

### Safe synchronization

Maintain `.paper-hexo-sync.json` in the paper workspace for articles managed by this workflow. Record the canonical HTML path and Hexo source path, relevant managed asset pairs, and SHA-256 hashes of both copies **after a successful synchronization**. Store paths, hashes and schema version only; avoid credentials. The record tracks ownership and last synchronized content, not publication status. Use paths relative to their respective roots where practical.

Before an update, compare both copies and the record:

- If the publication copy still matches its recorded hash, synchronize the canonical changes and refresh the record after verifying the result.
- If only the publication copy changed, or both copies changed, inspect and preserve those edits; reconcile them into the canonical version before overwriting. If intent cannot be resolved safely from context, show the conflict and ask the user. Do not silently discard either version.
- For an existing article with no record, compare the files. If identical, establish a record; if they differ, inspect and reconcile rather than declaring either copy authoritative by assumption. This includes older articles edited directly in Hexo, such as the existing SGLang article.
- Apply the same checks to managed assets. Do not delete unrelated files, overwrite existing articles or URLs, or replace a changed asset simply because its path matches. Report unresolved conflicts rather than claiming synchronization succeeded.

## Workflow

1. **Discover.** Identify the paper, desired operation (create, revise, local preview, or publish), relevant article and Hexo paths, existing metadata, assets, and any synchronization record. Respect unrelated worktree changes. Use the previously agreed concise, readable style when appropriate; ask only when missing information prevents progress.
2. **Analyze or update.** For a new explainer, read the source paper sufficiently to understand its contributions, method, experiments and limitations, and check primary sources and relevant code as available. For an existing HTML, retain the user's design and scope. Keep source links traceable, mark uncertainty, and distinguish paper-era results from later code. Preserve useful legends and technical caveats when trimming repeated evidence notes.
3. **Review.** Apply the reviewer skill as appropriate. Check internal consistency, claims and figures against the source, references, anchors, responsive layout and text contrast. Prefer low-saturation, readable themes and a concise title hierarchy when consistent with user preferences; do not force a particular palette. Inspect actual desktop and narrow rendering when tools permit, and state any limits honestly.
4. **Integrate.** Inspect the Hexo site's established mechanism before creating files. For the current site, the article typically lives in `source/papers/<slug>/index.html` with a metadata post under `source/_posts/` using matching `link` and `html_source` values; `scripts/html-articles.js` injects navigation, reading-time estimate and Vercount markup. Reuse that mechanism if present; do not assume another site has it. Keep title, category, tags and summary appropriate to the article, and ensure list titles link directly to the full HTML. Preserve older public URLs with a compatible redirect when applicable. Ensure relative images, CSS, scripts and other article assets resolve without duplicating unrelated content.
5. **Build and verify.** Run the site's normal build and check the generated article, index/category/tag links, old redirects where relevant, paper links, in-page anchors and assets. Preview through a local server when needed because `file://` may not resolve root-relative resources. Confirm injected navigation and reading-time markup; an estimate is not actual dwell time. Verify the counter in a real browser when feasible, and identify third-party/network limitations rather than claiming it works from markup alone. Record sync hashes only after successful copy and verification.
6. **Deliver.** Report the canonical HTML, Hexo output/entry, checks and any unresolved issues. By default stop after local preparation and preview; if the user explicitly asks to deploy, follow the existing site workflow and report the actual result. A build, Git commit, push and website deployment are distinct actions; never describe one as another or claim a site is live without verification.
