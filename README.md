# EMBS Book

Embedded Systems Design and Implementation (EMBS) Module at the University of York.

**Website**: [https://embs-book.github.io/](https://embs-book.github.io/)

## Development

This site is built with [MkDocs](https://www.mkdocs.org/) and the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

### Local preview

```bash
pip install -r requirements.txt
mkdocs serve
```

Then open [http://127.0.0.1:8000](http://127.0.0.1:8000).

### Deployment

The site deploys automatically to GitHub Pages via GitHub Actions on push to `main`.

## Contents

- **Getting Started** — Module overview, lecture guide for lectures 0–18, and how to use this book
- **Embedded Systems Design** — Design metrics, model refinement and FPGA implementation
- **Specification** — Requirements, Ptolemy II, models of computation, KPN and SDF analysis
- **HW/SW Co-Design** — Introduction, design space exploration, partitioning and mapping
- **Embedded Software and RTOS** — Compilation, WCET, execution structures and FreeRTOS mechanisms
- **Real-Time Scheduling** — Task models, fixed-priority response-time analysis, EDF, PDA and QPA
- **Resource Sharing, Multicore and Mixed Criticality** — Blocking bounds, processor assignment and execution budgets
- **Interactive Tutorials**
  - [Kernighan-Lin Algorithm](https://embs-book.github.io/tutorials/kl-algorithm/)
  - [Diffusion Load Balancing](https://embs-book.github.io/tutorials/diffusion-algorithm/)
  - [PDA vs QPA](https://embs-book.github.io/tutorials/pda-qpa/)

## Content maintenance

Chapters identify the lectures they accompany and distinguish lecture examples from illustrative examples. Keep the lecture guide, homepage and `mkdocs.yml` navigation in sync when adding a chapter. The local `__ref/` directory contains teaching references and is excluded from version control and the published site.

Verify changes with `mkdocs build --strict`. Check numerical examples against their stated task model; keep assessment dates and weights in the official module information rather than copying them from individual slide editions.
