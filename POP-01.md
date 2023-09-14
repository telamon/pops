# POP-01 Identity

An individual's identity is a self generated keypair using curve `secp256k1` and `sha256` hashes.

On protocol level and in developer context HEX-string encoding is preferred; otherwise we use plain binary in tranmission.

- We do not specify any identity management, we trust users to hold their own keys.  
- We do not specify any recovery schemes, we trust users to learn (not-)lose their keys. 
