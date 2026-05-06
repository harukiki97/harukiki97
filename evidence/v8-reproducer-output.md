# Reproducer Evidence

This document contains the key runtime evidence for the V8 Wasm SIMD shuffle
reducer miscompilation. It focuses on the observed d8 output: baseline Wasm
execution versus optimized-tier execution, plus a control run with the affected
optimization disabled.

## Environment

| Item | Value |
| --- | --- |
| Engine | V8 d8 |
| Target | ARM64 release build |
| Reproducer | `poc-wasm-shuffle-security-impact.js` |
| Trigger | Explicit Wasm tier-up with `--allow-natives-syntax` |
| Control flag | `--nowasm-simd-opt` |

## Vulnerable Run

<details open>
<summary>baseline vs optimized divergence</summary>

```text
$ out/arm64.release/d8 --allow-natives-syntax poc-wasm-shuffle-security-impact.js

branch baseline: 0x00001111 mem[1]=0x01 mem[3]=0x42
index baseline: 0x00001111 mem[1]=0x01 mem[3]=0x42
write baseline: 0x000042aa mem[1]=0xaa mem[3]=0x42
read baseline: 0x00000001 mem[1]=0x01 mem[3]=0x42
large-offset baseline: 0x00000001 mem[1]=0x01 mem[3]=0x00
trap baseline: 0x00000001 mem[1]=0x01 mem[3]=0x00

branch optimized: 0x00003333 mem[1]=0x01 mem[3]=0x42
index optimized: 0x00003333 mem[1]=0x01 mem[3]=0x42
write optimized: 0x0000aa01 mem[1]=0x01 mem[3]=0xaa
read optimized: 0x00000042 mem[1]=0x01 mem[3]=0x42
large-offset optimized: 0x0101ff01 mem[1]=0x01 mem[3]=0x00
trap optimized: threw RuntimeError: memory access out of bounds

branch: DIVERGED
index: DIVERGED
controlled write: DIVERGED
controlled read: DIVERGED
large offset: DIVERGED
trap behavior: DIVERGED

Error: security impact PoC: branch/index/memory/trap divergence
```

</details>

## Control Run

The same reproducer was run with Wasm SIMD optimization disabled.

<details open>
<summary>no divergence with --nowasm-simd-opt</summary>

```text
$ out/arm64.release/d8 --nowasm-simd-opt --allow-natives-syntax \
    poc-wasm-shuffle-security-impact.js

branch baseline: 0x00001111 mem[1]=0x01 mem[3]=0x42
index baseline: 0x00001111 mem[1]=0x01 mem[3]=0x42
write baseline: 0x000042aa mem[1]=0xaa mem[3]=0x42
read baseline: 0x00000001 mem[1]=0x01 mem[3]=0x42
large-offset baseline: 0x00000001 mem[1]=0x01 mem[3]=0x00
trap baseline: 0x00000001 mem[1]=0x01 mem[3]=0x00

branch optimized: 0x00001111 mem[1]=0x01 mem[3]=0x42
index optimized: 0x00001111 mem[1]=0x01 mem[3]=0x42
write optimized: 0x000042aa mem[1]=0xaa mem[3]=0x42
read optimized: 0x00000001 mem[1]=0x01 mem[3]=0x42
large-offset optimized: 0x00000001 mem[1]=0x01 mem[3]=0x00
trap optimized: 0x00000001 mem[1]=0x01 mem[3]=0x00

branch: did not diverge
index: did not diverge
controlled write: did not diverge
controlled read: did not diverge
large offset: did not diverge
trap behavior: did not diverge
no divergence observed
```

</details>

## Interpretation

The vulnerable run shows that the same Wasm functions produce different results
after optimization:

| Signal | Baseline | Optimized |
| --- | --- | --- |
| branch | `0x00001111` | `0x00003333` |
| table index | `0x00001111` | `0x00003333` |
| controlled write | writes `mem[1]` | writes `mem[3]` |
| controlled read | reads `mem[1] == 0x01` | reads `mem[3] == 0x42` |
| large offset | `0x00000001` | `0x0101ff01` |
| trap behavior | returns normally | traps out of bounds |

The control run shows that disabling Wasm SIMD optimization removes the
divergence. This isolates the mismatch to the Wasm SIMD optimization path rather
than general Wasm execution.

## Related Files

- `poc-wasm-shuffle-security-impact.js`
- `poc-wasm-shuffle-byte-demand.js`
- `poc-wasm-shuffle-web-triggerable-impact.js`
- `README.portfolio.md`
- `README.portfolio.ko.md`
