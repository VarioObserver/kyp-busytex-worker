# KYP LaTeX Worker Host — v22 companion

This companion explains the hosting change required by the v22 Blogger template.

## Why this exists

The Blogger page is not a reliable place to bootstrap a nested worker from a GitHub Release URL. Release downloads redirect to asset hosts, and a Worker script is more sensitive to URL, MIME type, CSP, and cross-origin behavior than ordinary links. The v22 template therefore supports a separately hosted `busytex_worker.js` entrypoint.

## Important

This starter package does **not** include TeXlyre's licensed/runtime build artifacts. Download them from the official BusyTeX build release and follow that project's license and release instructions:
https://github.com/TeXlyre/texlyre-busytex-build

The upstream project documents these files:
- `busytex_pipeline.js`
- `busytex_worker.js`
- `busytex.wasm`
- `busytex.js`
- `texlive-basic.js` and `.data`
- `texlive-recommended.js` and `.data`
- `texlive-extra.js` and `.data`

## Deploy

1. Create a public GitHub repository, e.g. `kyp-latex-worker`.
2. Put the upstream `busytex_worker.js` at the repository root or `/assets/busytex_worker.js`.
3. Enable GitHub Pages from the `main` branch.
4. Confirm the raw page URL returns JavaScript, not a GitHub HTML page. Example URL shape:
   `https://YOUR-USERNAME.github.io/kyp-latex-worker/busytex_worker.js`
5. In the Blogger template, find `KYP_TEX_CONFIG` and set:
   `workerScriptUrl: 'https://YOUR-USERNAME.github.io/kyp-latex-worker/busytex_worker.js'`
6. The v22 template injects the configured URL into its dedicated compiler worker automatically, so only the `KYP_TEX_CONFIG.workerScriptUrl` setting needs to be changed.
7. Test the exact file in a browser tab. Then run the starter LaTeX document and inspect the Runtime / Download Activity panel.

## CORS and asset paths

A same-origin worker entrypoint solves only the worker-script bootstrap. WASM/data files still need to be fetched with valid CORS responses and correct paths. If the worker expects `busytex.wasm` relative to its own URL, publish the corresponding assets at those expected paths or patch the asset base according to upstream documentation. Do not assume that publishing only one JS file is sufficient.

## Recommended long-term solution

Use a dedicated static compiler app on GitHub Pages (or another static host) with the worker and runtime assets served from one controlled origin, then embed that app into Blogger using an iframe. This is more robust than trying to make a multi-worker WebAssembly toolchain behave from inside a large Blogger XML template.
