# POP-04: HTML Container (Runlevel 0)

This Specification defines format for a web-page embedded within a block.

The first bytes must be `html0` followed by a linebreak `LF`.  
The zero denotes runlevel.

Then followed by an optional _Headers_ section or a second linebreak `LF` to indicate the start of body.

The `block.size` contains total bytes in block thus it is safe  
to assume the size of html-document equals `block.size - firstOccurrenceOf("\n\n")`

**Example**

```html
html0
Public-Key: <PublicKey HEXString64>
Date: 1679920149342
Revision: 1
Source: https://myweb.silo1.tld
Source: https+silo://silo1.tld
Source: https+silo://silo2.tld

<html>
  <head>
    <title>Page Title</title>
    <style>
      body { color: purple; }
    </style>
  </head>
  <body>
    <h3>Welcome to my website</h3>
    <p>Leave a note in the guestbook:</p>
    <tbd-thread />
  </body>
</html>
```

## Headers

All header names are treated as case-insenstivie.  
Some header values may be case-sensitive.


#### Public-Key `optional`
May be attached when stored on spacious devices, or might be presented separately.  
If present, then block signature must be verifiable using this key.  

#### Date `recommended`
A UTC Time-stamp in milliseconds.

#### Revision `recommended`
Integer increment, when signing a new version of a previous page this counter must be incremented.

#### Source `recommended`
Source header can be repeated multiple times to hint alternative
locations for the website should one become unavailable.


## Body

HTML

## Limits

#### Size 64K (R0-R2)

Entire block body (excluding signature and/or parent signature) must not exceed 64 Kilo Bytes

#### Script

Runlevel 0 does not support `<script>` tags.
If a page contains a script tag it is considered invalid for that runlevel


## Run Levels
The runlevel defines the complexity a runtime must provide 
in order to present a user experience.

| Runlevel | Size  | Custom Elements | Scripting |
|----------|-------|-----------------|-----------|
| 0        | 64 kB | no              | no        |
| 1        | 64 kB | yes             | no        |
| 2        | 64 kB | yes             | yes       |
| 3        | 4 MB  | yes             | yes       |
| 4        | 16 MB | yes             | yes       |

## Custom Elements `TBD POP-0401`

Are higher-order widgets, that solve a particular recurring use-case.
They must be completely optional but are intended to offer
a lo-tech approach for site-owners to get started.  
Any occuring normalization is completely accidental.

- Topic based chat widget
- Cart & Payment widget
- Sign Document widget

