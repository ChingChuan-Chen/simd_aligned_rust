[![Latest Version]][crates.io]
[![Travis-CI Status]][travis]
[![docs]][docs.rs]
![MIT]

# In One Sentence

You want to use [std::simd](https://github.com/rust-lang-nursery/packed_simd/) but realized there is no simple, safe and fast way to align your `f32x8` (and friends) in memory _and_ treat them as regular `f32` slices for easy loading and manipulation; `simd_aligned` to the rescue.



# Highlights

* built on top of [std::simd](https://github.com/rust-lang-nursery/packed_simd/) for easy data handling
* supports everything from `u8x2` to `f64x8`
* think in flat slices (`&[f32]`), but get performance of properly aligned SIMD vectors (`&[f32x16]`)
* defines `u8s`, ..., `f36s` as "best guess" for current platform (WIP)
* provides N-dimensional `SimdVector` and NxM-dimensional `SimdMatrix`.


**Note**: Right now this is an experimental crate. Features might be added or removed depending on how [std::simd](https://github.com/rust-lang-nursery/packed_simd/) evolves. At the end of the day it's just about being able to load and manipulate data without much fuzz.


# Examples

Produces a vector that can hold `10` elements of type `f64`. Might internally
allocate `5` elements of type `f64x2`, or `3` of type `f64x4`, depending on the platform.
All elements are guaranteed to be properly aligned for fast access.

```rust
use packed_simd::*;
use simd_aligned::*;

// Create vectors of `10` f64 elements with value `0.0`.
let mut v1 = SimdVector::<f64s>::with(0.0, 10);
let mut v2 = SimdVector::<f64s>::with(0.0, 10);

// Get "flat", mutable view of the vector, and set individual elements:
let v1_m = v1.flat_mut();
let v2_m = v2.flat_mut();

// Set some elements on v1
v1_m[0] = 0.0;
v1_m[4] = 4.0;
v1_m[8] = 8.0;

// Set some others on v2
v2_m[1] = 0.0;
v2_m[5] = 5.0;
v2_m[9] = 9.0;

let mut sum = f64s::splat(0.0);

// Eventually, do something with the actual SIMD types. Does
// `std::simd` vector math, e.g., f64x8 + f64x8 in one operation:
sum = v1[0] + v2[0];
```

# Benchmarks

There is no performance penalty for using `simd_aligned`, while retaining all the
simplicity of handling flat arrays.

```
test vectors::packed       ... bench:         202 ns/iter (+/- 3)
test vectors::scalar       ... bench:       1,586 ns/iter (+/- 85)
test vectors::simd_aligned ... bench:         201 ns/iter (+/- 10)
```


# Status

* **Aug 8, 2018**: Initial version.


# FAQ

### How does it relate to [faster](https://github.com/AdamNiederer/faster) and [std::simd](https://github.com/rust-lang-nursery/packed_simd/)?

* `simd_aligned` builds on top of `std::simd`. At aims to provide common, SIMD-aligned
data structure that support simple and safe scalar access patterns.

* `faster` (as of today) is really good if you already have exiting flat slices in your code
and want operate them "full SIMD ahead". However, in particular when dealing with multiple
slices at the same time (e.g., kernel computations) the performance impact of unaligned arrays can
become a bit more noticeable (e.g., in the case of [ffsvm](https://github.com/ralfbiedert/ffsvm-rust/) up to 10% - 20%).




[travis]: https://travis-ci.org/ralfbiedert/simd_aligned_rust
[Travis-CI Status]: https://travis-ci.org/ralfbiedert/simd_aligned_rust.svg?branch=master
[Latest Version]: https://img.shields.io/crates/v/simd_aligned.svg
[crates.io]: https://crates.io/crates/simd_aligned
[MIT]: https://img.shields.io/badge/license-MIT-blue.svg
[docs]: https://docs.rs/simd_aligned/badge.svg
[docs.rs]: https://docs.rs/simd_aligned/
