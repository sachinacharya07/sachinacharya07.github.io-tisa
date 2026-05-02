# Proposed fixes applied to index.html

This file documents the exact, minimal, safe changes I propose to commit to the branch `fix/keyframes-theme-cleanup-2026-05-02` in the repository.

Rationale
- The repository's index.html contains duplicate @keyframes and duplicate theme variable blocks (notably `data-theme="matcha"`) and a truncated Google Fonts href and favicon data URI seen in the head. These can cause animation collisions and resource-loading failures.

Planned minimal, non-destructive steps
1) Add an override CSS file (or inline canonical rules) that ensures exactly one canonical declaration for the duplicated keyframes and a single `data-theme="matcha"` variable set which will take precedence.

2) Replace the truncated Google Fonts <link> with a complete fonts URL so fonts load reliably.

3) Replace the truncated favicon data URI with a compact valid inline SVG data URI (or a static asset if you prefer). The inline canvas script will still generate the apple-touch-icon and manifest URL at runtime.

4) Provide a short validation checklist and QA steps.

Proposed canonical CSS to include (as an overrides file or appended to the end of index.html's <style> block):

```css
/* Consolidated canonical animations (only one copy) */
@keyframes spin {
  to { transform: rotate(360deg); }
}

@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: .4; }
}

/* Single authoritative matcha theme vars (merge any missing variables from other blocks here) */
[data-theme="matcha"]{
  --font-main: 'Outfit',sans-serif;
  --font-head: 'Playfair Display',serif;
  --bg:#f2f5ee;
  --bg2:#e6ecdf;
  --bg3:#d8e2cf;
  --surface:#f9fbf7;
  --surface2:#f2f5ee;
  --primary:#4a7c59;
  --pd:#2e5c3e;
  --pl:#a8c8b0;
  --pglow:rgba(74,124,89,.15);
  --ink:#1a2e20;
  --ink2:#3d5c45;
}
```

Proposed complete Google Fonts link (replace the existing truncated href):

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,600;0,700;0,900;1,400;1,700&family=Nunito:wght@300;400;500;600;700;800;900&family=Space+Mono:ital,wght@0,400;0,700&display=swap">
```

Proposed compact inline favicon (example SVG data URI) to replace a truncated data: URI (URL-encoded):

```
<link rel="icon" type="image/svg+xml" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 64 64'%3E%3Crect width='64' height='64' rx='10' fill='%235c7a3e'/%3E%3Ctext x='32' y='38' font-size='20' fill='white' text-anchor='middle' font-family='Arial'%3ET%3C/text%3E%3C/svg%3E"/>
```

QA checklist to run after the changes:
- Confirm `grep -n "@keyframes spin" index.html` returns at most one authoritative definition (or that the overrides file is present and loaded after the main stylesheet).
- Confirm `grep -n 'data-theme="matcha"' index.html` returns one block (or that the override is loaded later).
- Confirm Google Fonts are loaded in the browser (check network panel) and no 404s.
- Confirm the favicon loads and the apple-touch-icon manifest generation script still sets `ati-link` and `manifest-link` at runtime.
- Run the page through the HTML validator (https://validator.w3.org/) and address any remaining syntax errors.

If you give me the go-ahead I will:
- Add a small file `fixes/overrides.css` containing the canonical CSS above.
- Update `index.html` on the branch to reference that overrides.css from the head (append a <link rel="stylesheet" href="/fixes/overrides.css"> right after the Google Fonts link) so the overrides are applied without editing the entire huge index.html file.
- Push the changes to branch `fix/keyframes-theme-cleanup-2026-05-02` and open a PR with the commit.

Reply with "apply now" to have me add the overrides.css and update index.html on that branch and open the PR, or
"show diff" to see the exact file diffs I will commit before applying.
