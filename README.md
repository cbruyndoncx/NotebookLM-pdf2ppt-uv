# NotebookLM-pdf2ppt-uv

Single-file PDF-to-PowerPoint converter using [uv](https://docs.astral.sh/uv/). Converts NotebookLM PDF exports to `.pptx` with vector graphics quality preserved.

Uses a **PDF → SVG → EMF → PPTX** pipeline — no rasterization, no quality loss.

## Quick Start

```bash
uv run pdf2ppt.py input.pdf
```

That's it. `uv` handles all Python dependencies automatically.

## System Dependencies

Two system tools must be installed before running:

| Platform | Command |
|----------|---------|
| **macOS** | `brew install pdf2svg inkscape` |
| **Ubuntu/Debian** | `sudo apt install pdf2svg inkscape` |
| **Fedora/RHEL** | `sudo dnf install pdf2svg inkscape` |
| **Arch** | `sudo pacman -S pdf2svg inkscape` |
| **Alpine** | `apk add pdf2svg inkscape` |
| **Windows** | `choco install pdf2svg inkscape` |

Verify they're installed:

```bash
uv run pdf2ppt.py --check-deps
# {"ok": true, "pdf2svg": true, "inkscape": true}
```

## Usage

```bash
# Basic conversion
uv run pdf2ppt.py input.pdf

# Custom output path
uv run pdf2ppt.py input.pdf output.pptx

# Remove NotebookLM watermark
uv run pdf2ppt.py input.pdf --remove-watermark

# Convert specific pages with parallel processing
uv run pdf2ppt.py input.pdf --pages 1-5,7 --parallel 4

# Overwrite existing output
uv run pdf2ppt.py input.pdf --force
```

### All Options

```
pdf2ppt.py [-h] [-v] [--check-deps] [--verbose] [--no-clean] [--no-check]
            [--force] [--pages PAGES] [--remove-watermark] [--parallel N]
            [--pdf2svg-path PATH] [--inkscape-path PATH]
            [input] [output]
```

| Flag | Description |
|------|-------------|
| `--check-deps` | Check system dependencies and exit (JSON output) |
| `--remove-watermark`, `--rw` | Remove NotebookLM watermark before conversion |
| `--pages`, `-p` | Page range, e.g. `1-5,7,9-11` |
| `--parallel`, `-j` | Parallel workers for SVG→EMF (default: 1) |
| `--force`, `-f` | Overwrite existing output file |
| `--no-clean` | Keep temporary files after conversion |
| `--no-check` | Skip SVG filter/transparency check |
| `--verbose` | Verbose output |

### Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | Generic error (file exists, file not found) |
| 2 | Missing system dependencies |
| 101 | PDF→SVG conversion failure |
| 102 | SVG→EMF conversion failure |

## AI Agent Usage

This script is designed to be invoked by AI agents/harnesses. The recommended workflow:

```bash
# 1. Check dependencies (machine-readable JSON)
uv run pdf2ppt.py --check-deps

# 2. Install if needed (adapt to the execution environment)
sudo apt install pdf2svg inkscape

# 3. Convert
uv run pdf2ppt.py input.pdf --remove-watermark --force
```

## How It Works

1. **PDF → SVG** — `pdf2svg` extracts each page as a vector SVG
2. **SVG → EMF** — `inkscape` converts SVGs to Enhanced Metafile (vector format PowerPoint understands)
3. **EMF → PPTX** — `python-pptx` assembles slides with correct dimensions and PDF metadata

Watermark removal (optional) uses PyMuPDF to detect the NotebookLM watermark in the bottom-right corner and fills it with sampled background colors, preserving gradients.

## Credits

Based on [notebookLM2PPT](https://github.com/neosun100/notebookLM2PPT) by neosun100.

## License

MIT
