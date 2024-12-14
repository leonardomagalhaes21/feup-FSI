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
In our case the flag obtained was: flag{bmwtblvtnbjcelod}.

# Task 2

### Evaluating the Offset in `cipherspec.py`

#### Objective
To determine the offset size in `cipherspec.py` that would render brute-forcing the key infeasible for personal computers over a 10-year period. The evaluation is based on experimentally calculating the number of keys that can be tested within a short duration and extrapolating to 10 years. Parallelization is ignored in this calculation.

#### Experimental Methodology
   - The script was executed on a personal computer.
   - The brute force script was modified to track the number of keys tested per second, keys testable in 10 years, and the minimum offset required.

The script used looks like this:

```python
from itertools import product
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
from binascii import unhexlify
import time

# Fixed part of the key (13 bytes of zeros as per cipherspec.py)
fixed_part = b'\x00' * 13

# Nonce and ciphertext provided in hexadecimal
nonce = unhexlify("0893e471df5011a854c486098c5ae7c6")
ciphertext = unhexlify("d010a56a6230ad26ccf9387878bde941763779dacba3")

# Track how many keys are tested
tested_keys = 0
start_time = time.time()

# Iterate through all possible combinations of the last 3 bytes of the key
for key_suffix in product(range(256), repeat=3): 
    key = fixed_part + bytes(key_suffix)  # Construct the full key
    cipher = Cipher(algorithms.AES(key), modes.CTR(nonce))
    decryptor = cipher.decryptor()

    try:
        # Try to decrypt the ciphertext
        plaintext = decryptor.update(ciphertext) + decryptor.finalize()
        # Check if the plaintext matches the expected flag format
        if plaintext.startswith(b"flag{") and plaintext.endswith(b"}"):
            print(f"Flag encontrada: {plaintext.decode()}")
            break
    except Exception:
        continue  # Ignore any errors during decryption
    
    # Increment the tested keys count
    tested_keys += 1
    # Print progress every 100,000 keys
    if tested_keys % 100000 == 0:
        elapsed_time = time.time() - start_time
        print(f"Keys tested: {tested_keys}, Time elapsed: {elapsed_time:.2f}s")

# Calculate and print the performance
elapsed_time = time.time() - start_time
keys_per_second = tested_keys / elapsed_time
print(f"Keys tested per second: {keys_per_second}")

seconds_in_10_years = 10 * 365.25 * 24 * 60 * 60  # 10 years in seconds
keys_in_10_years = keys_per_second * seconds_in_10_years
print(f"Keys testable in 10 years: {keys_in_10_years}")

required_keyspace = keys_in_10_years + 1  # Ensure keyspace exceeds testable keys
required_offset = 0
while 256**required_offset < required_keyspace:
    required_offset += 1
print(f"Minimum offset required: {required_offset}")

```
#### Results obtained

- **Keys per second**: `77382`
- **Seconds in 10 years**: `10 * 365.25 * 24 * 3600 = 315576000 seconds`
- **Total keys testable in 10 years**: `77382 * 315576000=24419913961709`

Given the AES key size (`16 bytes`), the total key space is `2^(8 * offset)` for a given offset. For the attack to be infeasible:

$$
offset  > \frac{log2(2441991396170)}{8}
$$

or

$$
offset  > \frac{44.512}{8} ≈ 5.564
$$



#### Conclusion
To ensure the encryption is secure against brute-force attacks over a 10-year period, the offset size must be at least **6 bytes** (rounding up the calculated value), which confirms the output obtained in our code. Using a smaller offset would expose the system to potential brute-force vulnerabilities given the computational power of modern personal computers. This analysis underscores the importance of choosing a sufficiently large offset for secure key generation.





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


