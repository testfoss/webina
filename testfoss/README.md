# Webina Testfoss archived-UI demo

The demo is the unmodified Webina 1.0.5 interface built from this archived
source. It explicitly uses the checked-in prebuilt Vina 1.2.3 artifacts in
`src/Webina/vina.js`, `src/Webina/vina.wasm`, and
`src/Webina/vina.worker.js`; the webpack build does not compile Vina from C++.

The browser scenario clicks **Use Example Files**, runs the stock 1xdn/ATP
example with one CPU, exhaustiveness 1, seed 1, and one output mode, then checks
the generated PDBQT pose, Vina affinity table, and output controls. Output stays
in the browser and is never saved into the repository.

## Run

From the repository root with Node.js 24 and Python 3:

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
SERVER_PID=$!
trap 'kill "$SERVER_PID"' EXIT
TESTFOSS_URL=http://127.0.0.1:8000 lmscript testfoss/browser.lms
```

If Playwright cannot download a browser on your host, set
`TESTFOSS_CHROMIUM=/absolute/path/to/chrome` in the final command's environment
to use an existing Chromium/Chrome executable instead.

The temporary `say` shim replaces only the final macOS notification command in
upstream `utils/build/after_build.sh`. It patches neither the build script nor
any UI/CSS file. Open `http://127.0.0.1:8000/testfoss/` for the additive landing
page or `http://127.0.0.1:8000/dist/` for the archived UI.

## Licensing and limits

`LICENSE.md` remains Apache-2.0. Upstream `package.json` declares ISC; that
metadata discrepancy is documented here without changing either file. This is a
smoke test of the archived UI and prebuilt Wasm, not a docking-accuracy or
cross-browser benchmark. Generated `dist/`, `webina.zip`, `node_modules/`, PDBQT
output, and browser artifacts must not be committed.
