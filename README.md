# GhostScramble

[![GhostScramble](ghostscramble.jpg)](https://github.com/williamstaffordparsons/ghostscramble)

## Table of Contents

- [Introduction](README.md?tab=readme-ov-file#introduction)
- [Author](README.md?tab=readme-ov-file#author)
- [License](README.md?tab=readme-ov-file#license)
- [Implementation](README.md?tab=readme-ov-file#implementation)
- [Parallelism](README.md?tab=readme-ov-file#parallelism)
- [Randomness Test Results](README.md?tab=readme-ov-file#randomness-test-results)
- [Speed Test Results](README.md?tab=readme-ov-file#speed-test-results)

## Introduction

GhostScramble is the hyper-efficient non-cryptographic PRNG (for 64-bit architecture) that has a minimum period of at least 2⁶⁴, excellent randomness test results, hyper-fast speed, low-footprint implementation, parallelism and reversibility (state rewinding).

## Author

GhostScramble was created by [William Stafford Parsons](https://github.com/williamstaffordparsons) as a product of [SeedFlurry](https://seedflurry.github.io).

## License

GhostScramble is licensed with the [BSD-3-Clause](LICENSE) license.

## Implementation

GhostScramble was implemented in C (requiring the `stdint.h` header to define a 64-bit, unsigned integral type for `uint64_t`).

[ghostscramble.c](ghostscramble.c)

#### `ghostscramble64`

The `ghostscramble64` function modifies the state in a `struct ghostscramble64_state` instance to generate a pseudorandom `uint64_t` integer as the return value.

Each variable (`a`, `b` and `c`) in a `struct ghostscramble64_state` instance must be [seeded](https://seedflurry.github.io) before generating a deterministic `ghostscramble64` sequence (that must discard the first few `ghostscramble64` results as a state warmup).

#### `ghostscramble128`

The `ghostscramble128` function modifies the state in a `struct ghostscramble128_state` instance to generate 2 pseudorandom `uint64_t` integers in the `output` array.

Each single-letter variable (`a`, `b` and `c`) in a `struct ghostscramble128_state` instance must be [seeded](https://seedflurry.github.io) before generating a deterministic `ghostscramble128` sequence (that must discard the first few `ghostscramble128` results as a state warmup).

#### `ghostscramble256`

The `ghostscramble256` function modifies the state in a `struct ghostscramble256_state` instance to generate 4 pseudorandom `uint64_t` integers in the `output` array.

Each single-letter variable (`a`, `b`, `c` and `d`) in a `struct ghostscramble256_state` instance must be [seeded](https://seedflurry.github.io) before generating a deterministic `ghostscramble256` sequence (that must discard the first few `ghostscramble256` results as a state warmup).

## Parallelism

Each GhostScramble variant enables a set of up to 2⁶⁴ parallel sequences (that each have non-probabilistic full state collision avoidance with each other for a period of at least 2⁶⁴) without Philox-style key variables (that could affect randomness quality consistency) or unnecessary jump-ahead functions (each parallel GhostScramble sequence has a minimum distance of 2⁶⁴ output results between each other).

Each instance within a set of parallel GhostScramble instances must adhere to the following single-letter variable seeding rules before generating parallel GhostScramble sequences.

1. `a` must be seeded with an integer that's unique among the set of parallel GhostScramble instances.
2. `b` must be seeded with an integer that's consistent among the set of parallel GhostScramble instances.
3. The remaining single-letter state variables must be [seeded](https://seedflurry.github.io).

After seeding a set of parallel GhostScramble instances, reseeding (or tampering with) either `a` or `b` voids the aforementioned parallelism segmentation properties.

## Randomness Test Results

GhostScramble yields excellent results from statistical test suites for randomness quality.

Each GhostScramble variant uses straightforward chaotic state accumulation procedures to reduce the probability of hidden statistical biases from specific seeded states (that each have a minimum period of at least 2⁶⁴ from a Weyl sequence).

The following GhostScramble randomness test results seeded each state variable with `0` (not reseeding or tampering with GhostScramble state variables after seeding).

#### PractRand 0.96

Each GhostScramble variant had no `FAIL` results from `stdin64` tests (for up to at least the default maximum of 32TB).

#### Dieharder 3.31.1

Each GhostScramble variant had no `FAILED` results from `dieharder -Y 1 -a -g 200 -k 2` tests.

## Speed Test Results

Each of the following results log the fastest process execution speed (in milliseconds) among several repetitions of a PRNG speed test (using `gcc -O3` from an AMD A4-9120C) that generates (and hashes) 1 billion pseudorandom `uint64_t` integers in a `#pragma GCC unroll 0` loop.

| PRNG | Elapsed |
| --- | --- |
| **`ghostscramble256`** | **561ms** |
| **`ghostscramble128`** | **743ms** |
| `shishua_avx2` (`-mavx2`) | 866ms |
| `shishua_sse4` (`-msse4`) | 978ms |
| **`ghostscramble64`** | **1072ms** |
| `shishua_sse3` (`-msse3`) | 1147ms |
| `shishua_sse2` (`-msse2`)| 1154ms |
| `biski64` | 1292ms |
| `sfc64` | 1320ms |
| `xoshiro256_plus` | 1546ms |
| `xorshiftr128_plus` | 1654ms |
| `jsf64_2rotate` | 1718ms |
| `xoroshiro128_plus` | 1733ms |
| `mrsf64` | 1833ms |
| `jsf64_3rotate` | 1841ms |
| `mrc64` | 1862ms |
| `romu_trio` | 1894ms |
| `wob2m` | 1928ms |
| `mwc192` | 1997ms |
| `wyrand` | 2033ms |
| `xorshift64` | 2135ms |
| `shishua` | 2251ms |
| `xorshift128_plus` | 2260ms |
| *`xorwow` | 2882ms |
| `romu_mono` | 2982ms |
| *`pcg32_minimal` | 2983ms |
| *`pcg_oneseq_64_xsh_rr_32` | 2987ms |
| `mwc128` | 2998ms |
| *`lehmer_mcg32` | 3402ms |
| *`pcg_oneseq_64_xsh_rs_32` | 3404ms |
| *`lcg32` | 3409ms |
| `lehmer_mcg64` | 3413ms |
| `lcg64` | 3416ms |
| `pcg_oneseq_64_xsl_rr_rr_64` | 3928ms |
| `splitmix64` | 4385ms |
| `cwg64` | 4680ms |
| `cwg128` | 4757ms |
| `sfmt` (`-msse2`) | 5525ms |
| `pcg_oneseq_128_xsh_rr_64` | 6833ms |
| `mt19937_64` | 7126ms |
| *`squares32` | 7552ms |
| `pcg64_dxsm` | 7604ms |
| `pcg_oneseq_128_xsh_rs_64` | 7676ms |
| `philox4x64` | 9171ms |
| `squares64` | 9596ms |
| `tinymt64` | 16081ms |
| *`stdlib_rand` | 46083ms |

\* PRNGs that were marked with an asterisk casted each n-bit output integer to a `uint64_t` integer.
