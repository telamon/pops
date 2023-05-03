# POP-02: Binary Block and Chain
`Draft: v4.1`

Purpose:

 - A **verifiable** and **highly transferable** data container.
 - Flat Memory, no `memcpy` no `alloc`
 - Unrestricted user land.
 - Readable on all modern digital devices.
 - The simpler the better.

Contents:

This spec describes one _Fmt_ byte and two types of segments: _Key_ and _Block_.

If repeated in a buffer they form a _Chain_ of linked blocks and keys.

## Key
Public keys are presented in their binary form and prefixed with the ASCII char `°`
```
°<32bytes Public Key>
```

<small>Hex-encoded sample</small>
```
6aee2f22cacb2e49bbb0d54ff1d9d912323787d81f08e73bb61a215d04029299a1
```
<!-- Secret:
f1d0ea8c8dc3afca9766ee6104f02b6ea427f1d24e3e4d6813b09946dff11dfa
-->

## Fmt

Fmt is short for Format.
It is a single byte and hints the loader at which offsets data can be expected.

- High nibble (bits 4..7) always `1011`
- Low nibble (bits 0..3) contain flags:
    - `0: type` unset: Key | set: Block
    - `1: genesis` unset: `psig` is 64-zeroes | set: `psig` follows `sig`
    - `2: phat` (unset: `Size` is u16 | set: `Size` is u32)
    - `3: tail` (unset: End of Chain | set: more segments follow)

<small>Samples</small>

```
key_segment[0]  = base2 1011 0000 = '°'

first_block[0]  = base2 1011 0001 = '±'
second_block[0] = base2 1011 0011 = '³'
tail_block[0]   = base2 1011 1011 = '»'

solo_block[0]   = base2 1011 1001 = '¹'

big_block[1]    = base2 1011 1101 = '½'
```

## Block Segment
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
504943302909649b2b6c323c19095b2bc69f1992e41e61e7364a048f07510b82046919be79be50c6bcd29cb6da13185446991d630bedef2326eaccc7ef0e8ebe7ff36c652500046861636b
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
6aee2f22cacb2e49bbb0d54ff1d9d912323787d81f08e73bb61a215d04029299a12109649b2b6c323c19095b2bc69f1992e41e61e7364a048f07510b82046919be79be50c6bcd29cb6da13185446991d630bedef2326eaccc7ef0e8ebe7ff36c652500046861636b2b14b5e829984a3dcd62f9983a56aa4f6eb6ba0a2c62e0b382fbf1b674a45b69b725ea41ce9622ffa1c35c3ff251d7dac4fbfa744cc76afbd84557a869544cef5a09649b2b6c323c19095b2bc69f1992e41e61e7364a048f07510b82046919be79be50c6bcd29cb6da13185446991d630bedef2326eaccc7ef0e8ebe7ff36c65250006706c616e6574
```

_If you can decode this, well done.  
The loader is complete!_

<!-- Junk
---
## visual summary

![Fig 1.](./fig/pop-02.png)
-->
