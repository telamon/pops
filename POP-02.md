# POP-02: Binary Block and Chain
`Draft: v5`
Proposal: replace bit 0 of fmt with `recoveryBit` of block-signature; 
Which removes the need for a key-segment type altogether.

`Draft: v4.1`

Purpose:

 - A **verifiable** and **highly stashable** data container.
 - Flat Memory, no `memcpy` no `alloc`
 - Unrestricted user land.
 - Readable on all modern digital devices.
 - The simpler the better.

Contents:

This spec describes one _Fmt_ byte and two types of segments: _Key_ and _Block_.

If repeated in a buffer they form a _Chain_ of linked blocks and keys.

## Key

Public keys are stored in their binary form and prefixed with the ASCII char `°`

```
°<32bytes Public Key>
```

<small>Hex-encoded sample</small>

```
b0ee2f22cacb2e49bbb0d54ff1d9d912323787d81f08e73bb61a215d04029299a1
```

<!-- Secret:
f1d0ea8c8dc3afca9766ee6104f02b6ea427f1d24e3e4d6813b09946dff11dfa
-->

## Fmt

Fmt is short for Format.
It is a single byte and hints the loader at which offsets data can be expected.

- High nibble (bits 4..7) always `1011`
- Low nibble (bits 0..3) contain flags:
<!--
Not sure which is less cluttered
    - `0: type` unset: Key | set: Block
    - `1: genesis` unset: `psig` is 64-zeroes | set: `psig` follows `sig`
    - `2: phat` (unset: `Size` is u16 | set: `Size` is u32)
    - `3: tail` (unset: End of Chain | set: more segments follow)
-->

| bit | flag    | unset (0)     | set (1)              |
|-----|---------|---------------|----------------------|
| 0   | type    | Key           | Block                |
| 1   | genesis | no parent     | `psig` follows `sig` |
| 2   | phat    | `size` is u16 | `size` is u32        |
| 3   | tail    | not last      | End of Chain         |

<small>Samples</small>

```
key_segment[0]  = base2 1011 0000 = '°'

first_block[0]  = base2 1011 0001 = '±'
second_block[0] = base2 1011 0011 = '³'
tail_block[0]   = base2 1011 1011 = '»'

solo_block[0]   = base2 1011 1001 = '¹'

big_block[1]    = base2 1011 1101 = '½'
```

## Block

A block contains user-land data and a signature.  
It might contain a parent signature if required by application.

<small>Layout</small>
```
[fmt][sig][psig][size][body]
```

- `fmt` One byte format of blocks
- `sig` Signature = `sign(blake3(psig + size + body), secret)`
- `psig` Parent Signature: optional, 64 zeros denotes genesis block
- `size` two or four bytes. Lite 64KB / Phat (soft limit 16MB)
- `body` a buffer of data with length equal to `size`.

Note: `sig` is in some contexts synonymous with `BlockID`

<small>Hex-encoded sample</small>

```
b98c4279609ff6b59ccd37272d6ea94b48025526b75b994476c96748883e23ca3ce1a7808ad8866b5a1db9d4d859431cc5f15b5a8ad0bbf82b3096b2029e92e8b400046861636b
```
<!-- TMI
### Block Header

The header starts with one byte that specifies the block format:

**Section offsets**

| Type         | Fmt Bin   | Fmt Char | @SIG | @PSIG | @SIZE | @BODY |
|--------------|-----------|----------|------|-------|-------|-------|
| Lite Genesis | 0010 0001 | !        | 1    | n/a   | 65    | 67    |
| Phat Genesis | 0010 0011 | #        | 1    | n/a   | 65    | 69    |
| Lite Child   | 0010 0101 | %        | 1    | 65    | 129   | 131   |
| Phat Child   | 0010 0111 | '        | 1    | 65    | 129   | 133   |
| KEY (32B)    | 0110 1010 | k        | n/a  | n/a   | n/a   | n/a   |
-->

## Chain

<small>Layout</small>

```
| MAGIC | KEY | BLOCK | BLOCK | KEY | BLOCK |
```

A chain is a buffer that contains a set of keys and blocks  
where each subsequent block _Parent Signature_ equals the  
signature of previous block.

When a chain is stored or transfered in an unlikely medium,
then the start of chain can be denoted using the following magic-bytes:

| Magic | Hex            | Description                   |
|-------|----------------|-------------------------------|
| PIC0  | 80, 73, 67, 48 | Pico Internet Chain version 0 |

The magic-sequence should be ignored when loading.

<small>Hex-encoded sample (chain with 1 key and 2 blocks)</small>

```
50494330b0ee2f22cacb2e49bbb0d54ff1d9d912323787d81f08e73bb61a215d04029299a1b18c4279609ff6b59ccd37272d6ea94b48025526b75b994476c96748883e23ca3ce1a7808ad8866b5a1db9d4d859431cc5f15b5a8ad0bbf82b3096b2029e92e8b400046861636bbb43c9faf94770e7f38f5cbb5c4987412077ef5586844be9f76f9f9fe552c211a7a663afcc9d96f37435ae2f1732d2a5b7c4a31012094cc40581413f76da4d2dd18c4279609ff6b59ccd37272d6ea94b48025526b75b994476c96748883e23ca3ce1a7808ad8866b5a1db9d4d859431cc5f15b5a8ad0bbf82b3096b2029e92e8b40006706c616e6574
```

_If you can decode this, well done.  
The loader is complete!_

<!-- Junk
---
## visual summary

![Fig 1.](./fig/pop-02.png)
-->
