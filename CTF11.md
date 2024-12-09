# CTF Semana #11 (Weak Encryption)

# Task 1: Understanding the Problem

In the provided `cipherspec.py` file, the gen() function introduces a critical vulnerability: it generates keys where the first 13 bytes are fixed to zero (\x00), leaving only the last 3 bytes as random. This drastically reduces the keyspace from 2^128 (for AES-128) to 2^24, making brute force feasible even on personal computers.

## How to Use the Cipher Suite to Encrypt and Decrypt Data?

### Encryption:


```python
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from cipherspec import enc, gen

key = gen()
nonce = os.urandom(16)  # Generate a 16-byte nonce
message = b"flag{example}"  # Message to encrypt
ciphertext = enc(key, message, nonce)
```
### Decryption:

```python
from cipherspec import dec
decrypted_message = dec(key, ciphertext, nonce)
```

## How to Exploit the Vulnerability?

Since only the last 3 bytes of the key are random, we can brute force the key by:

1. Iterating through all 2^24 possible values for the random part of the key.

2. Decrypting the ciphertext for each key guess.

3. Checking if the decrypted message matches the expected `flag{}` format.

## How to Automate the Process?

```python
import os
from itertools import product
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from binascii import unhexlify

# Provided nonce and ciphertext
nonce = unhexlify("0893e471df5011a854c486098c5ae7c6")
ciphertext = unhexlify("d010a56a6230ad26ccf9387878bde941763779dacba3")

# Fixed part of the key
fixed_part = b'\x00' * 13

# Brute force the last 3 bytes
for key_suffix in product(range(256), repeat=3):
    key = fixed_part + bytes(key_suffix)

    cipher = Cipher(algorithms.AES(key), modes.CTR(nonce))
    decryptor = cipher.decryptor()
    try:
        plaintext = decryptor.update(ciphertext) + decryptor.finalize()
        if plaintext.startswith(b"flag{") and plaintext.endswith(b"}"):
            print(f"Flag found: {plaintext.decode()}")
            break
    except Exception:
        continue

```

This code performs a brute-force attack to recover the encryption key for an AES-CTR cipher. It systematically tests all possible combinations for the last 3 bytes of the 16-byte key, as the first 13 bytes are fixed to zeros. Using each key, it attempts to decrypt the ciphertext with the provided nonce. If the decrypted text matches the expected format flag{...}, the correct key is identified, and the hidden flag is revealed. This process exploits the predictable structure of the key generation to break the encryption.

## How to Validate the Flag?
The correct flag will be a string in the format flag{xxxxxxxx}, the script stops as soon as a matching flag is found.

# Task 2

To determine the offset required to make brute force infeasible within 10 years, we analyze the brute force rate.

### Measuring Speed
...
### Calculation for 10 Years
...



# Task 3 

### Problems with a 1-Byte Nonce

Reducing the nonce size to 1 byte introduces several severe vulnerabilities:

Limited Nonce Space:
        A 1-byte nonce only has 2^8=256 possible values.
        Nonce reuse is highly likely, leading to predictable patterns in the ciphertext.

XOR Differential Attacks:
        When the same nonce is reused with different plaintexts, an attacker can XOR the ciphertexts to deduce information about the plaintexts.

Increased Brute Force Feasibility:
        Instead of testing one key per ciphertext, an attacker would test 256 keys per ciphertext to account for all nonce possibilities. This marginally increases computation but does not add meaningful security.


