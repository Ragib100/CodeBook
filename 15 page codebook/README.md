# Reference Document — Overleaf project

LaTeX source for the printed reference document (MIST CodeCrafters 2.0).
Built from `rifat_codebook.docx`, plus the Bellman-Ford code added under Graph Theory.

**Current size: 15 A4 pages — the limit, with no room left.** Anything you add
now has to come out of something else. `extras.tex` no longer fits.

The page count here is from a local build. Overleaf has `inconsolata`, which is
narrower than the fallback typewriter font, so it may come out a little shorter --
check the count in Overleaf after your first compile.

## Files

| File | What it is |
|---|---|
| `main.tex` | Preamble, page geometry, header, listing style, index. Compile this one. |
| `formulas.tex` | The Formula section (area, triangles, trigonometry, sums, logs, series, facts), typeset as real LaTeX math. |
| `probability.tex` | Probability and expected-value formulas. |
| `content.tex` | Every code section, generated from the docx. |
| `extras.tex` | Sections that were dropped only to fit 15 pages. Not included by default. |
| `main.pdf` | The current build, for reference. |

## Using it on Overleaf

1. Overleaf → **New Project → Upload Project**, and upload the four `.tex` files
   (zip them first, or create a blank project and upload the files into it).
2. Menu → **Compiler: pdfLaTeX**, **Main document: main.tex**.
3. Recompile until the numbers settle — the index and the `Page x / y` total each
   need an extra pass (three runs from a clean state). Overleaf runs `latexmk`,
   which repeats automatically; a manual `pdflatex` needs to be run three times.

No custom packages are needed; everything used ships with Overleaf's TeX Live.
`inconsolata` is loaded only if it exists, so the project also builds on a minimal
local TeX install (it falls back to the default typewriter font).

## Contest rules this is built to satisfy

- A4, single sided, **≤ 15 pages** — check the page count in the PDF after every edit
- **Name + student ID upper-left**, set in `main.tex`:
  `\fancyhead[L]{... Md. Ragib Hossain Rifat -- 202314022}`
- **Page number upper-right**: `\fancyhead[R]{Page \thepage\ / \pageref{LastPage}}`
- Readable at 0.5 m: code is 9 pt mono, the same size as the body text and the
  formulas. Set by `basicstyle=\ttfamily\fontsize{9.0}{9.7}` in `main.tex`.
  Dropping it to 8.5 pt buys back about a page if you need the room.

## Adding a new algorithm

Open `content.tex`, find the right `\section`, and add:

```latex
\subsection{Name of the algorithm}
\begin{lstlisting}
your code here, verbatim - no escaping needed
\end{lstlisting}
```

Sections and subsections are numbered automatically, so you never have to renumber
anything by hand, and the index picks the new entry up on the next compile.
Keep the source ASCII: smart quotes and en-dashes pasted from Word will break the
build (`’ — × ≤` and friends). Plain `' - x <=` are fine.

## Turning on the extras

`extras.tex` holds what is left out for space: power set, Kadane, tree
depth/width, all-subarray-sum, meet in the middle, and N-Queen. The document is
now at 15 pages, so these no longer fit — including them needs the code font
dropped to 8.5 pt first. To include them, uncomment this line in `main.tex`:

```latex
%\input{extras}
```

At 9 pt that overflows the limit; at 8.5 pt it fits.

## Correctness pass

The listings were checked against the compiler rules once they were out of Word.
Fixed: `int and = 1` in binaryExp, LCIS and MO's algo (`and` is a C++ keyword, and
`ans` was never declared); `s[i] - base` in Trie (the constant is `BASE`);
`it d[1001]` in SPFA; `ll left = first[u],;` in LCA; comment text that had spilled
onto uncommented lines in SPFA and Convex Hull; the `Addition:`/`Subtraction:`
labels in Modular Operation and other prose that sat inside code blocks;
`#define multiply(a,b)` whose body had been split onto a second line.

Two logic bugs: Segmented Sieve initialised `isPrime[R-L+1]`, one past the end of
the array, and nCr (less space) called `binaryExp` with two of its three arguments.

Two content changes: the SOS DP section held only the O(4^n) brute force, so the
real O(n * 2^n) recurrence was added below it; and the section named "Eulerian
circuit" is a subtree flatten (tin/tout), so it is now called "Euler Tour".

## Notes on the pasted-in code

**Useful Things** now holds, after Fast I/O: `Debug` (the `__print`/`debug`
template), `Stress test`, and `VS Code setup`. All three were reflowed from the
PDF-copied text, which had wrapped mid-token. One line copied out of the PDF into
the middle of the stress test was the page header itself
(`MIST ERS - Military Institute of Science and Technology 14`); it is not code and
was dropped. The stress test also gained a two-line comment naming the two
binaries it shells out to (`brute`, `fast`), since it calls them before they are
mentioned anywhere.

**Knapsack** lost three lines that had drifted into the top of the section in the
Word file (`int l[100]= {0}, int r[100]= {0};`, a stray `stack<int> mystack;`, and
a truncated `int n = 0, w`) — none of them are part of the algorithm — along with
the `#include`/`using` header and the demo `main()`. The two functions are intact.

**Bellman-Ford** sits under Graph Theory, right after Dijkstra. Two changes to
what was supplied:

- `for (int step = 0; step < num_of_nd; step)` → `step++`. Without the increment
  the loop never terminates; the missing `++` looks like a copy-paste loss.
- Added a two-line comment naming the globals it needs (`adj`, `dist`, `parent`,
  `inf`) and the reminder to initialise `dist[]` to `inf` before calling.
