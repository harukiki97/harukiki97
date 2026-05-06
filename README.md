# Jinwoo Han

> WebAssembly runtime security, browser engine bug analysis, and full-stack product development.

[![WebAssembly](https://img.shields.io/badge/WebAssembly-654FF0?style=flat-square&logo=webassembly&logoColor=white)](https://webassembly.org/)
[![V8](https://img.shields.io/badge/V8%20%2F%20Turboshaft-4B8BF5?style=flat-square&logo=googlechrome&logoColor=white)](https://v8.dev/)
[![C++](https://img.shields.io/badge/C%2B%2B-00599C?style=flat-square&logo=cplusplus&logoColor=white)](https://isocpp.org/)
[![React](https://img.shields.io/badge/React%20%2F%20TypeScript-61DAFB?style=flat-square&logo=react&logoColor=111111)](https://react.dev/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)

Hello, I am **Jinwoo Han**, a developer focused on WebAssembly runtime security, browser engine behavior, compiler optimization bugs, and full-stack product development.

I work on low-level issues where small implementation assumptions become reproducible evidence: validator soundness gaps, runtime invariants, sanitizer findings, compiler semantic divergence, and upstream-ready bug reports. I also build user-facing web products with React, TypeScript, Spring Boot, and cloud infrastructure.

## Currently

- Hyundai AutoEver Mobility SW School, 3rd cohort, in progress through June 2026
- Researching WebAssembly runtime and browser engine security
- **Open to opportunities**: security engineer and full-stack developer roles

## Evidence Highlights

| Area | Evidence |
| --- | --- |
| WasmEdge security research | On the vulnerable revision, ASan reported a host-side `ValueStack` heap OOB write. On the patched tree, the same malformed path fails closed with `IndirectCallTypeMismatch`. |
| V8 compiler bug analysis | Reproduced branch, table index, memory read/write, and trap behavior divergence between baseline and optimized tiers. The divergence disappears with the `--nowasm-simd-opt` control run. |
| PINT full-stack project | Visual evidence of a photo-first masonry feed using real post preview data, hover metadata, and like feedback UI. |
| Patent-pending architecture | **Leviathan Engine**, a WebAssembly-based deterministic memory shredding architecture for sensitive data in browser-based managed runtimes. Formalities review passed. |

## Visual Evidence

### WasmEdge Reproducer Evidence

On the vulnerable revision, a malformed active element segment using typed function references passed validation and reached executor paths. Under ASan, this reproduced a host-process `ValueStack` heap OOB write. On the patched tree, the same malformed path is blocked with `IndirectCallTypeMismatch`, showing fail-closed behavior.

![WasmEdge reproducer evidence](./evidence/wasmedge-reproducer-evidence-full.svg)

- Upstream fix: [WasmEdge PR #4814](https://github.com/WasmEdge/WasmEdge/pull/4814)
- Status: upstream fix merged, GHSA/CVE advisory process in progress
- Key point: the observed memory-safety impact was in the executor's host-side `ValueStack`, not guest linear memory

### V8 Reproducer Evidence

I reproduced an ARM64 miscompilation in V8 Turboshaft's Wasm SIMD shuffle reducer. The demanded-byte analysis underestimated required input bytes when demand propagated through a low-half widening operation.

![V8 reproducer evidence](./evidence/v8-reproducer-evidence-full.svg)

- Chromium issue: [508025392](https://issues.chromium.org/issues/508025392)
- V8 fix CL: [7806230](https://chromium-review.googlesource.com/c/v8/v8/+/7806230)
- Merged commit: [`f56988e0207f8a4ce295b46ad4c50367140f899e`](https://chromium.googlesource.com/v8/v8/+/f56988e0207f8a4ce295b46ad4c50367140f899e)
- Full reproducer output: [v8-reproducer-output.md](./evidence/v8-reproducer-output.md)

<details>
<summary>Key V8 divergence signals</summary>

```text
branch baseline:   0x00001111
branch optimized:  0x00003333

index baseline:    0x00001111
index optimized:   0x00003333

write baseline:    mem[1] = 0xaa
write optimized:   mem[3] = 0xaa

read baseline:     0x00000001
read optimized:    0x00000042

trap baseline:     returns normally
trap optimized:    RuntimeError: memory access out of bounds
```

The same reproducer does not diverge when run with `--nowasm-simd-opt`, isolating the issue to the Wasm SIMD optimization path.

</details>

### PINT Masonry Feed Evidence

**PINT** is a photo-first community platform for sharing where a photo was taken and how it was edited. The screenshot below shows a masonry feed built from real post preview data, including hover metadata, author/location/camera information, and like feedback UI.

![PINT masonry feed evidence](./evidence/pint_masonry_feed_evidence.png)

- Frontend role: React 19, TypeScript, Vite, Zustand, Tailwind CSS, GSAP
- Contributions: Masonry-style feed, infinite scrolling, image preload and animation flow, feed card hover interactions, like micro-interactions, profile state synchronization
- Links: [Frontend](https://github.com/Team-Pint/Pint-Frontend), [Backend](https://github.com/Team-Pint/Pint-Backend), [Live Demo](https://pint-frontend-three.vercel.app/login)

## Featured Work Summary

| Project | Summary |
| --- | --- |
| WasmEdge Validator Soundness Vulnerability | Found a typed function reference validation gap and reproduced malformed Wasm module acceptance, runtime invariant breakage, and host-side heap OOB write. Upstream PR and regression tests were merged. |
| V8 Turboshaft Wasm SIMD Compiler Bug | Found an ARM64 optimized-tier miscompilation that violated WebAssembly SIMD semantics. Tracked the report through Chromium Issue Tracker and V8 Gerrit until the fix landed. |
| Leviathan Engine | Patent-pending security architecture that tracks sensitive bytes in WebAssembly linear memory and deterministically overwrites exact allocation ranges. |
| PINT | Photo location and filter-sharing platform. Frontend work included masonry feed behavior, interaction design, SPA auth UX, and team collaboration. |

## Technical Focus

```text
Security / Runtime
WebAssembly, WasmEdge, V8, Turboshaft, runtime validation, compiler correctness,
ASan, UBSan, malformed module construction, responsible disclosure

Frontend
React, TypeScript, Vite, Zustand, Tailwind CSS, GSAP, SPA authentication UX,
photo-first interaction design

Backend / Infra
Java, Spring Boot, Spring Security, PostgreSQL, Redis, AWS S3, EC2, Nginx,
Docker, Vercel, session and CSRF-based authentication
```

## Contact

- Email: [qwejinoohan@gmail.com](mailto:qwejinoohan@gmail.com)
- GitHub: [@harukiki97](https://github.com/harukiki97)
- Open to opportunities: security engineer and full-stack developer roles
- Main keywords: `WebAssembly`, `V8`, `WasmEdge`, `Runtime Security`, `Compiler Correctness`, `React`, `TypeScript`, `Spring Boot`
