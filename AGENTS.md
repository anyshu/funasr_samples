# Repository Guidelines

## Project Structure & Module Organization
- `python/`: Python WebSocket clients and `requirements_client.txt` for minimal deps.
- `cpp/`: C++ WebSocket clients (`websocket_client/`) with CMake build scripts.
- `java/`: Java WebSocket client with `Makefile`.
- `html/`: Browser demo (`static/index.html`) for mic/file input.
- `audio/`: Sample WAV/PCM/MP4 assets used by clients during local checks.

## Build, Test, and Development Commands
- Python: `pip3 install -r python/requirements_client.txt` (websockets), then `python python/funasr_wss_client.py --host <ip> --port 10095 --mode 2pass --audio_in audio/asr_example.wav`.
- C++: `cd cpp/websocket_client && mkdir -p build && cd build && cmake -DCMAKE_BUILD_TYPE=Release .. && make`, then `./bin/funasr-wss-client --server-ip <ip> --port 10095 --wav-path ../../audio/asr_example.wav`.
- Java: `cd java && make downjar && make buildwebsocket && make runclient` (uses `asr_example.pcm` by default).
- HTML: open `html/static/index.html` in a browser and point it to the ASR server; enables mic or file upload.
- Use sample files in `audio/` to sanity-check offline/online/2pass modes before submitting changes.

## Coding Style & Naming Conventions
- Python: 4-space indents, snake_case for functions/vars, keep argparse flag names consistent with existing clients.
- C++: follow current client style (lower_snake filenames, minimal headers, prefer explicit flags); keep SSL and hotword options aligned with help text.
- Java: PascalCase classes, lowerCamelCase methods/fields; keep CLI flag names stable.
- Shell snippets in docs should be copy-pasteable from repo root unless noted.

## Testing Guidelines
- No automated test suite; rely on manual runs per language. For features touching multiple clients, validate at least one Python and one compiled client.
- For streaming changes, test `online` and `2pass` modes; for batch changes, test `offline`.
- Capture and share a short sample output JSON when reporting test results.

## Commit & Pull Request Guidelines
- Commits: imperative, scoped prefixes help (e.g., `python: add ssl toggle`, `cpp: fix chunk defaults`); avoid bundling unrelated files.
- PRs: include a brief summary, commands executed, modes tested, and sample outputs; link to issues/tasks when available. Screenshots of HTML UI changes are helpful.
- Keep generated binaries/build folders out of commits; prefer documenting build steps instead.

## Security & Configuration Tips
- Do not hardcode server hosts, ports, tokens, or SSL cert paths; use CLI flags (`--host`, `--port`, `--ssl`) and document defaults.
- SSL is enabled by default (`--ssl 1` / `--is-ssl 1`); only disable for local trusted endpoints.
- Hotword files often contain sensitive terms—keep them out of version control.***
