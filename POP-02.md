# Binary Block and Chain

A block is a cryptographically signed container for data.
Every piece of information should be encoded and stored in this form.

It is represented as a flat buffer with 4 components:

```
[64B Signature][64B Parent Signature][u32 Size][BODY]
```

- Signature = sign(Parent + Size + Body)
- Parent Signature: optional, 64 zeros denotes genesis block
- Size: four bytes for implementational convenience. Blocks should not exceed 16777216 (16MB)
- Body: a buffer of content with length equal to Size.


# Chain
> This part of the specification is **optional**, a block can be
> transported/persisted in any container capable of holding it.

A chain is a flat buffer that contains a set of keys and blocks  
where each subsequent block _Parent Signature_ equals the  
signature of it's predecessor.

This is the preferred way to transmit data between nodes.


The chain contains markers denoting the start of a Key or Block.

| Marker | Size | Description                    |
|--------|------|--------------------------------|
| PIC0.  | 5    | PicoInternetChain v0           |
| K0.    | 3    | Key                            |
| B0.    | 3    | Block                          |
| B1.    | 3    | Block without Parent Signature |

All keys required to validate the chain _should_ be included, but a key _must_ never
occur before a block that depends on it.

Example:

```
PIC0.K0<32-bytes Public Key>B0.<132B+ Block>
```

