# Archived Webina Testfoss scenario

## Baseline and prebuilt artifacts

Upstream is Webina `4230729e7dad197b4c77912dffe30c8bcdd492ba` (1.0.5).
The repository already tracks the AutoDock Vina 1.2.3 WebAssembly port. CI does
not rebuild that C++ codebase and does not download a replacement binary.

SHA-256 at the baseline:

- `src/Webina/vina.wasm`:
  `e17a8807aee230e629cdafa6f31897d2bbd993f5c3a3374b84d0638aaa2927f4`
- `src/Webina/vina.js`:
  `944b160a4978326cbc237390f0e7e727adbcb63521a713ec16f6e6b0446d7934`
- `src/Webina/vina.worker.js`:
  `280f56fb60bf72545a6e68b55f11174ef5b90e8ff23fa9d9a7c4431d4f43aba8`

The workflow checks that webpack's `dist/Webina/vina.wasm` is byte-identical to
the checked-in Wasm before launching the browser.

## Build and browser invocation

Run `npm ci`, then `npm run build` with an ephemeral directory containing an
executable no-op `say` at the front of PATH. This handles only the upstream
build's final macOS notification. Do not patch `utils/build/after_build.sh`.

Serve the repository using `testfoss/server.py`, which supplies COOP/COEP for
SharedArrayBuffer. Run `TESTFOSS_URL=http://127.0.0.1:8000 lmscript
testfoss/browser.lms`. The `.lms` binds `cli="lmwebtest playwright"` and clicks
the archived UI's stock example. It uses CPU 1, exhaustiveness 1, seed 1, and
one mode to keep CI practical while still executing Vina. It asserts a generated
PDBQT model and atom records, a Vina affinity table, and output controls.

Tool pins: lmctl 0.2.469, Playwright 1.63.0, lmwebtest v0.1.10 at commit
`2920befd355eacc654697820d690db074e94dce3` (Linux x64 SHA-256
`b90cb79fc2b00627da226e563f77a395fedcb95aca01b076af8ff14755009690`).
The exact setup and server lifecycle commands are in `AGENTS.md` and
`testfoss/README.md`.

## Licensing and limitations

Apache-2.0 in `LICENSE.md` coexists with an ISC declaration in upstream
`package.json`. This fork documents but does not resolve that discrepancy.
Neither file is modified.

The check does not establish docking accuracy, determinism across native Vina
versions, mobile support, or non-Chromium compatibility. It saves no output
PDBQT. `dist/`, `webina.zip`, dependencies, Wasm builds, and browser output are
ephemeral and excluded from the additive change.

## Remote verification

GitHub Actions run 42651538978 passed the unmodified archived UI build, byte
comparison of the checked-in Vina engine, and real stock-example docking through
lmwebtest.
