# Complexity Lab

An interactive teaching tool for **algorithmic complexity attacks**: cases where an attacker supplies input that pushes an algorithm into its worst-case running time, so a small request consumes a large amount of CPU. It runs from a single file, `complexity-lab.html`, with no installs, no build step, no network requests and no libraries.

> **Safety note:** Complexity Lab only *counts operations* in an in-page simulation. It does not time or attack any real system, and it has no network code. The hash function is a deliberately simple toy that exists only to make the concept visible. Nothing here targets a real library, framework or server.

## Quick start

1. Download `complexity-lab.html`.
2. Open it in any modern browser. It works offline.

## Learning goals

After using the tool, a learner should be able to explain:

- The difference between average-case and worst-case running time, and why attackers care about the worst case.
- Why a hash table's O(1) lookups depend on keys spreading across buckets, and how collisions turn it into O(n) per operation and O(n²) overall.
- Why quicksort's worst case depends on input order and pivot choice.
- Why the cost grows with n², so doubling the input roughly quadruples the work.
- Practical defenses: keyed or randomized hashing, randomized pivots, robust algorithms, and input size and time limits.

## Tab 1: Hash table collisions

This tab inserts keys into a chained hash table and counts the comparisons made while walking each bucket's chain.

| Control | What it does |
|---|---|
| **Number of keys** | Slider from 10 to 400. |
| **Buckets** | 16, 32 or 64 buckets. |
| **Key stream** | *Ordinary random keys*, or *keys chosen to collide with the toy hash* (all land in one bucket under the fixed toy hash). |
| **Hash function** | *Fixed public toy hash* (sum of character codes) or *Keyed with a secret seed* (a seeded hash an outsider cannot predict). |
| **New secret seed** | Picks a fresh seed, showing the defense does not depend on one lucky value. |

What you see:

- **Bucket chart**: one bar per bucket showing chain length. Bars turn orange when a bucket is much longer than expected.
- **Growth chart**: total comparisons as the number of keys grows, for random keys (green), colliding keys (orange), and the worst-case curve n(n−1)/2 (dashed). A dot marks your current setting.
- **Readout**: comparisons, the typical expected cost (n²/2B), the slowdown versus typical, and an illustrative time at 1 ns per comparison.
- **Banner** explaining whether the table is healthy, degraded, or protected by the keyed hash.

### Things to try

1. Leave settings on *Fixed public toy hash* and switch the key stream to *colliding keys*. One bar towers over the rest and the dot jumps to the orange curve.
2. Now switch to *Keyed with a secret seed* with the same colliding keys. The bars flatten. The keys were prepared against the public hash, but they are no longer special.
3. Press **New secret seed** several times. The result stays healthy.
4. With colliding keys and the fixed hash, double the key count and watch the comparisons roughly quadruple.

## Tab 2: Sorting worst case

This tab runs quicksort (Lomuto partition, iterative) on the chosen input and counts comparisons.

| Control | What it does |
|---|---|
| **Input size n** | Slider from 50 to 3000. |
| **Input order** | Random, already sorted, reverse sorted, or all equal. |
| **Pivot rule** | First element, random element, or median of three. |
| **Y axis** | Linear or log scale for the chart. |

What you see:

- **Input preview**: sampled bars showing the shape of the input.
- **Comparison chart**: your setting (indigo) against n·log₂n (green dashed, typical) and n²/2 (orange dashed, worst case).
- **Readout**: comparisons, n·log₂n, the ratio between them, and an extrapolated time for 100,000 items at 10 ns per comparison.
- **Banner** explaining whether the run was near n·log n or quadratic, and why.

### Things to try

1. *Already sorted* with *First element* pivot: the count lands on the n²/2 curve. Each partition removes only one element.
2. Keep *Already sorted* and change the pivot to *Random element* or *Median of three*. The count drops to near n·log n.
3. Choose *All equal*. Every pivot rule becomes quadratic with this partition scheme, which shows that fixing the pivot is not the only possible weakness.
4. Switch the Y axis to **Log** to see the gap between curves as orders of magnitude.

## Key idea: why this is a security problem

If an attacker controls the input and knows (or can guess) the algorithm, a short, cheap request can cost the server enormous work, causing denial of service. The same pattern appears in hash-table flooding, sorting, regular-expression matching, and parsing. This tool shows two instances and the pattern behind them.

## Defenses illustrated

- **Randomization or secrets** (keyed hashing, random pivots): the attacker cannot predict where work will concentrate.
- **Better worst-case algorithms**: for example balanced trees or introsort instead of plain quicksort.
- **Resource limits**: caps on input size, request time and per-client CPU.
- **Careful algorithm choice** for any code path fed by untrusted input.

## How it works (for readers of the code)

- **Single file** with HTML, CSS and JavaScript.
- **Deterministic generators**: a seeded pseudo-random generator (`prng`) makes key sets and inputs reproducible, so smaller sizes are consistent with larger ones.
- **Tab 1 functions**: `makeKeys` builds random or colliding keys, `toy` and `keyed` are the two hash functions, and `insertAll` counts chain walks.
- **Tab 2 functions**: `makeInput` builds the input orders and `qsort` counts comparisons using an explicit stack, so deep recursion cannot crash the page.
- **Charting**: `chart` is a small shared SVG line-chart helper with linear and log scales.
- **Theming**: CSS variables with automatic light and dark modes.

## Limitations

- It counts abstract operations. Real timing depends on hardware, caches, compilers and runtimes.
- The hash function is a toy. Real systems use stronger, often seeded, hashes, which is why the attack here is easy to describe.
- Only two examples are included. Regular-expression backtracking and timing side channels are natural next topics.

## Extending it

- Add regex backtracking: count steps for a pattern like `(a+)+$` as the input grows.
- Add an early-exit string comparison and show how step counts reveal a matching prefix.
- Add a "server budget" mode where requests per second are limited by CPU cost.
- Add other sort algorithms (merge sort, insertion sort) for comparison.

## Browser support

Any modern browser (current Chrome, Firefox, Safari, Edge).
