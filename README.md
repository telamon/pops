POPs
====

POPs is short for **Pico\* Overclock Protocols**.  

They're written with the purpose to document and implement _Transports_, _Silos_ and _Bootloaders_
of decentralized web-sites that can be run from anything.

[background/motivation](./pitch.md)

## Specs

- [POP-01: Identity](./POP-01.md)
- [POP-02: Binary block and chain](./POP-02.md)
- [POP-03: Portable String Encoding](./POP-03.md) TBD
- [POP-04: HTML container](./POP-04.md)
- [POP-05: Markdown container](./POP-05.md) TBD
- [POP-xx: Custom Elements](./POP-tbd.md) TBD


## Run Levels

The runlevel defines the complexity of a Bootloader and it's runtime.
It might also be seen as a minimum size-unit for Silos and bandwidth constraints.

| Runlevel | Size  | Custom Elements | Scripting |
|----------|-------|-----------------|-----------|
| 0        | 64 kB | no              | no        |
| 1        | 64 kB | yes             | no        |
| 2        | 64 kB | yes             | yes       |
| 3        | 4 MB  | yes             | yes       |
| 4        | 16 MB | yes             | yes       |

## Custom Elements

Are higher-order widgets, that solve a particular recurring use-case.
They must be completely optional but are intended to offer
a lo-tech approach for site-owners to get started.  
Any occuring normalization is completely accidental.

TBD
- Topic based chat widget
- Cart & Payment widget
- Sign Document widget

--

These documents are released as CC0 / Public Domain.  
Implementations may vary.
<!-- Belief systems are nice, everyone should have one. -->
