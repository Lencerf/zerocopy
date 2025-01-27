<!-- Copyright 2022 The Fuchsia Authors

Licensed under a BSD-style license <LICENSE-BSD>, Apache License, Version 2.0
<LICENSE-APACHE or https://www.apache.org/licenses/LICENSE-2.0>, or the MIT
license <LICENSE-MIT or https://opensource.org/licenses/MIT>, at your option.
This file may not be copied, modified, or distributed except according to
those terms.

WARNING: DO NOT EDIT THIS FILE. It is generated automatically. Edits should be
made in the doc comment on `src/lib.rs` or in `generate-readme.sh`.
-->

# zerocopy

*<span style="font-size: 100%; color:grey;">Need more out of zerocopy?
Submit a [customer request issue][customer-request-issue]!</span>*

***<span style="font-size: 140%">Fast, safe, <span
style="color:red;">compile error</span>. Pick two.</span>***

Zerocopy makes zero-cost memory manipulation effortless. We write `unsafe`
so you don't have to.

## Overview

Zerocopy provides four core marker traits, each of which can be derived
(e.g., `#[derive(FromZeros)]`):
- `FromZeros` indicates that a sequence of zero bytes represents a valid
  instance of a type
- `FromBytes` indicates that a type may safely be converted from an
  arbitrary byte sequence
- `IntoBytes` indicates that a type may safely be converted *to* a byte
  sequence
- `Unaligned` indicates that a type's alignment requirement is 1

Types which implement a subset of these traits can then be converted to/from
byte sequences with little to no runtime overhead.

Zerocopy also provides byte-order aware integer types that support these
conversions; see the `byteorder` module. These types are especially useful
for network parsing.

[customer-request-issue]: https://github.com/google/zerocopy/issues/new/choose

## Cargo Features

- **`alloc`**
  By default, `zerocopy` is `no_std`. When the `alloc` feature is enabled,
  the `alloc` crate is added as a dependency, and some allocation-related
  functionality is added.

- **`byteorder`** (enabled by default)
  Adds the `byteorder` module and a dependency on the `byteorder` crate.
  The `byteorder` module provides byte order-aware equivalents of the
  multi-byte primitive numerical types. Unlike their primitive equivalents,
  the types in this module have no alignment requirement and support byte
  order conversions. This can be useful in handling file formats, network
  packet layouts, etc which don't provide alignment guarantees and which may
  use a byte order different from that of the execution platform.

- **`derive`**
  Provides derives for the core marker traits via the `zerocopy-derive`
  crate. These derives are re-exported from `zerocopy`, so it is not
  necessary to depend on `zerocopy-derive` directly.

  However, you may experience better compile times if you instead directly
  depend on both `zerocopy` and `zerocopy-derive` in your `Cargo.toml`,
  since doing so will allow Rust to compile these crates in parallel. To do
  so, do *not* enable the `derive` feature, and list both dependencies in
  your `Cargo.toml` with the same leading non-zero version number; e.g:

  ```toml
  [dependencies]
  zerocopy = "0.X"
  zerocopy-derive = "0.X"
  ```

- **`simd`**
  When the `simd` feature is enabled, `FromZeros`, `FromBytes`, and
  `IntoBytes` impls are emitted for all stable SIMD types which exist on the
  target platform. Note that the layout of SIMD types is not yet stabilized,
  so these impls may be removed in the future if layout changes make them
  invalid. For more information, see the Unsafe Code Guidelines Reference
  page on the [layout of packed SIMD vectors][simd-layout].

- **`simd-nightly`**
  Enables the `simd` feature and adds support for SIMD types which are only
  available on nightly. Since these types are unstable, support for any type
  may be removed at any point in the future.

[simd-layout]: https://rust-lang.github.io/unsafe-code-guidelines/layout/packed-simd-vectors.html

## Security Ethos

Zerocopy is expressly designed for use in security-critical contexts. We
strive to ensure that that zerocopy code is sound under Rust's current
memory model, and *any future memory model*. We ensure this by:
- **...not 'guessing' about Rust's semantics.**
  We annotate `unsafe` code with a precise rationale for its soundness that
  cites a relevant section of Rust's official documentation. When Rust's
  documented semantics are unclear, we work with the Rust Operational
  Semantics Team to clarify Rust's documentation.
- **...rigorously testing our implementation.**
  We run tests using [Miri], ensuring that zerocopy is sound across a wide
  array of supported target platforms of varying endianness and pointer
  width, and across both current and experimental memory models of Rust.
- **...formally proving the correctness of our implementation.**
  We apply formal verification tools like [Kani][kani] to prove zerocopy's
  correctness.

For more information, see our full [soundness policy].

[Miri]: https://github.com/rust-lang/miri
[Kani]: https://github.com/model-checking/kani
[soundness policy]: https://github.com/google/zerocopy/blob/main/POLICIES.md#soundness

## Relationship to Project Safe Transmute

[Project Safe Transmute] is an official initiative of the Rust Project to
develop language-level support for safer transmutation. The Project consults
with crates like zerocopy to identify aspects of safer transmutation that
would benefit from compiler support, and has developed an [experimental,
compiler-supported analysis][mcp-transmutability] which determines whether,
for a given type, any value of that type may be soundly transmuted into
another type. Once this functionality is sufficiently mature, zerocopy
intends to replace its internal transmutability analysis (implemented by our
custom derives) with the compiler-supported one. This change will likely be
an implementation detail that is invisible to zerocopy's users.

Project Safe Transmute will not replace the need for most of zerocopy's
higher-level abstractions. The experimental compiler analysis is a tool for
checking the soundness of `unsafe` code, not a tool to avoid writing
`unsafe` code altogether. For the foreseeable future, crates like zerocopy
will still be required in order to provide higher-level abstractions on top
of the building block provided by Project Safe Transmute.

[Project Safe Transmute]: https://rust-lang.github.io/rfcs/2835-project-safe-transmute.html
[mcp-transmutability]: https://github.com/rust-lang/compiler-team/issues/411

## MSRV

See our [MSRV policy].

[MSRV policy]: https://github.com/google/zerocopy/blob/main/POLICIES.md#msrv

## Changelog

Zerocopy uses [GitHub Releases].

[GitHub Releases]: https://github.com/google/zerocopy/releases

## Disclaimer

Disclaimer: Zerocopy is not an officially supported Google product.
