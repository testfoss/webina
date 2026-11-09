# Testfoss fork guidance

This public archived demonstration fork is pinned to upstream Webina commit
`4230729e7dad197b4c77912dffe30c8bcdd492ba` on branch `testfoss-demo`.
Do not alter upstream UI, TypeScript, CSS, examples, build scripts, lockfiles,
or other workflows. Additions are limited to this file, the appended `README.md`
section, `durable-memory/**`, `testfoss/**`, and
`.github/workflows/testfoss.yml`.

## Prebuilt Vina boundary

The archived UI uses the checked-in prebuilt AutoDock Vina 1.2.3 artifacts
`src/Webina/vina.js`, `vina.wasm`, and `vina.worker.js`. Do not replace them or
claim that CI compiles Vina. Webpack compiles/copies the archived UI around
those artifacts. SHA-256 values are recorded in durable memory.

`LICENSE.md` is Apache-2.0 and must remain intact. Upstream `package.json`
separately declares ISC. Preserve and document that discrepancy; do not silently
relicense the project or edit either declaration as part of Testfoss work.

## Reproduce

Use Node.js 24 and Python 3. Full commands are in `testfoss/README.md`:

```bash
npm ci
mkdir -p "${TMPDIR:-/tmp}/testfoss-webina-bin"
printf '#!/bin/sh\nexit 0\n' > "${TMPDIR:-/tmp}/testfoss-webina-bin/say"
chmod +x "${TMPDIR:-/tmp}/testfoss-webina-bin/say"
PATH="${TMPDIR:-/tmp}/testfoss-webina-bin:$PATH" npm run build
npm ci --prefix testfoss
npm run --prefix testfoss browsers:install
lmscript install github:lmctl-ai/lmwebtest@v0.1.10
export PATH="$HOME/.cache/lmscript/tools/github/lmctl-ai/lmwebtest/v0.1.10:$PATH"
python3 testfoss/server.py --root . --port 8000 &
TESTFOSS_URL=http://127.0.0.1:8000 lmscript testfoss/browser.lms
```

The unmodified `npm run build` ends in macOS `say`; the ephemeral PATH shim is
the only Linux accommodation. Never patch `after_build.sh`, UI, or CSS for this.

## Exact browser pins

- `@lmctl-ai/lmctl` `0.2.426`.
- `lmwebtest` `v0.1.10`, commit
  `2920befd355eacc654697820d690db074e94dce3`; Linux x64 SHA-256
  `b90cb79fc2b00627da226e563f77a395fedcb95aca01b076af8ff14755009690`.
- Playwright `1.63.0`, locked under `testfoss/`.
- `actions/checkout@11d5960a326750d5838078e36cf38b85af677262` (v4.4.0) and
  `actions/setup-node@49933ea5288caeca8642d1e84afbd3f7d6820020` (v4.4.0).

The workflow has `contents: read`, a timeout, no secrets, and no deployment.
It runs real stock-example docking with CPU 1, exhaustiveness 1, seed 1, and one
mode. This is an archived-UI/Wasm smoke test, not scientific docking validation.
Never commit generated `dist/`, `webina.zip`, dependencies, browser downloads,
or output PDBQT files. Do not commit or push unless explicitly requested.
