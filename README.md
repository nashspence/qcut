# qcut

qcut is a containerised utility for assembling highlight reels from directories of source videos. The script mirrors the original shell workflow: probe durations, plan clip lengths, encode each segment with SVT-AV1, and append them into a final Matroska file with timestamp overlays.

The tool is configured exclusively through environment variables (no CLI flags). The most common variables are:

- `QCUT_SRC_DIR` (default `/in`)
- `QCUT_OUT_DIR` (default `/out`)
- `QCUT_TARGET_SECONDS` (default `600`)
- `QCUT_MIN_SECONDS` / `QCUT_MAX_SECONDS` (defaults `6` / `9`)
- `QCUT_SVT_PRESET`, `QCUT_SVT_CRF`, `QCUT_SVT_LP` (defaults `4`, `38`, `5`)
- `QCUT_OPUS_BR` (default `128k`)
- `QCUT_TRUE_PEAK_DB` (optional limiter ceiling)
- `QCUT_FONT_FILE` (default container font)
- `QCUT_DEBUG_CMDS` and `QCUT_VERBOSE` (set to `1` to enable)

## Repository layout

- `Containerfile` – build definition for the runtime image.
- `qcut.py` – main entrypoint for the tool.
- `spec.md` – Gauge specification describing expected behaviour.
- `tests/` – unit tests for the script.

## Development

Set up a virtual environment and install the tooling you need for development:

```bash
python -m venv .venv
. .venv/bin/activate
pip install -U pip
pip install pre-commit pytest
```

Run the automated checks before submitting changes:

```bash
pre-commit run --all-files
pytest
```

Build and run the container image locally to exercise the full workflow:

```bash
podman build -t qcut .
podman run --rm \
  -v "$PWD/in:/in:ro" \
  -v "$PWD/out:/out" \
  -e QCUT_SRC_DIR=/in \
  -e QCUT_OUT_DIR=/out \
  -e QCUT_SVT_LP=6 \
  qcut
```

## Release

Cut a semantic version tag to publish a new container image to GitHub Container Registry:

```bash
git tag -a v1.2.3 -m "v1.2.3"
git push origin v1.2.3
```

The release workflow builds multi-architecture images and pushes them to `ghcr.io/nashspence/qcut` with the tag name, `latest`, and the Git commit SHA.
