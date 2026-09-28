# How to Use This Book

This book is designed as a companion resource for the EMBS module. Here's how to get the most out of it.

---

## Navigation

- Use the [lecture guide](lecture-guide.md) to find the reading and examples for lectures 0–18
- Use the **sidebar** on the left to browse chapters and sections
- Use the **search bar** at the top to find specific topics
- Use the **table of contents** on the right to jump within a page
- Toggle between **light and dark mode** using the icon in the header

## Study a chapter

Read the task or system assumptions before applying an equation. Work through the examples by hand, then expand the self-check answers. Distinguish a sufficient test's inconclusive failure from an exact test's rejection under its stated model.

For design work, record a chain of evidence: requirement → model → implementation decision → analysis or measurement. The [FPGA chapter](../embedded-systems-design/fpga.md) shows how this connects to the practicals.

---

## Interactive Tutorials

Throughout this book, you'll find interactive tutorials that let you explore algorithms and concepts visually. These are designed to complement the lecture material.

!!! tip "Try it yourself"
    Interactive tutorials work best when you experiment with them. Don't just read — click, drag, and explore!

Current interactive tutorials:

- [**Kernighan-Lin Algorithm**](../tutorials/kl-algorithm.md) — Step through the KL graph partitioning algorithm
- [**Diffusion Load Balancing**](../tutorials/diffusion-algorithm.md) — Step through iterative load diffusion on a graph
- [**PDA vs QPA**](../tutorials/pda-qpa.md) — Compare Processor Demand Analysis with Quick convergence Processor-demand Analysis

---

## Conventions

Throughout this book, we use the following conventions:

!!! note
    Notes provide additional context or clarification.

!!! warning
    Warnings highlight common pitfalls or important considerations.

!!! example
    Examples illustrate concepts with concrete scenarios.

`Code snippets` appear in monospace font, and longer code blocks are displayed as:

```c
// Example C code
int main() {
    printf("Hello, Embedded World!\n");
    return 0;
}
```

---

## Feedback

If you find any errors or have suggestions for improvement, please open an issue on the [GitHub repository](https://github.com/embs-book/embs-book.github.io/issues).
