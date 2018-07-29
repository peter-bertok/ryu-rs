# Overview
A Rust version of the Ryū float-to-string conversion algorithm by Ulf Adams of Google Germany.  

## Description
The purpose of this crate is to improve the speed of converting `f32` and `f64` to string representations, 
which is particularly important when performing high-performance serialisation of JSON, CSV, XLSX, and 
scientific data formats where all numbers are internally treated as floating point. In some cases, this
can be the bottleneck to serialisation.

During development of this code it was noted that the [serde_json](https://github.com/serde-rs/json/) crate
references the [dtoa](https://github.com/dtolnay/dtoa) crate, which is both slower and contains unsafe
code. This crate uses no unsafe code, and could potentially use `#![no_std]` as well.

## Status
Things are looking promising, with the Ryū `bench_write_f32_shortest` function absolutely
smoking the currently available alternatives:

```
test tests::bench_dtoa               ... bench:          45 ns/iter (+/- 1)
test tests::bench_f32_debug          ... bench:         108 ns/iter (+/- 0)
test tests::bench_f32_format         ... bench:         109 ns/iter (+/- 0)
test tests::bench_write_f32_shortest ... bench:          25 ns/iter (+/- 0)
```

The `f32` version of the function has been exhaustively tested in version 0.1.1 of the crate, guaranteeing that all possible 2^32 float values can round-trip with no loss of precision when converted using this library:

```
...
99.930916% complete
99.95419% complete
99.97748% complete
test test_exhaustive_roundtrip ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

A test harness for the `f64` version will be written soon, but the testing can't be exhaustive, as it would take years to run...

## References
* [Ryū: Fast Float-to-String Conversion (acm.org)](http://delivery.acm.org/10.1145/3200000/3192369/pldi18main-p10-p.pdf?ip=203.220.129.155&id=3192369&acc=OA&key=4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E4D4702B0C3E38B35%2E66AE2C137E5E05CA&__acm__=1532737655_ac5e7ac0d74d1a28eaf5dd975900f935)
* [ryu/f2s.c](https://github.com/ulfjack/ryu/blob/master/ryu/f2s.c)
* [ryu/d2s.c](https://github.com/ulfjack/ryu/blob/master/ryu/d2s.c)
