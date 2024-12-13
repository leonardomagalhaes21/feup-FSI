# CTF Semana #12 - (RSA)

## Understand the Challenge Parameters

We started by analyzing the provided RSA setup:

1.	Public Key: 
- modulus:119477202134466932562563268982823685174093386217657810644912885880702534757992106097239968629214788656866883802487322356532146500693429981738932845488692628293094673772453581148536651475011432263161744028861026196959738807441815153690687119150438113868535572665743017326891030515480059986743952870186268308272847161091535234421043628647351429721
- public exponent: 65537

2.	Encrypted Message: 6529642360750bd83ab84fa7d793fd0fedb4caa9b2e4118bb2032858012d69f206e507d8636f6aaec8569670bd70014e241ec3e96555fdbaa67c9adbd73d8a8c59c01d4d63ae5f70f7a6c9478d6daeb679520e62ae7f90995c03d6e5665cba81e92619063fc1215321034e37ac4304ad366433d5f27a60b1224c95df9d465b2ac2c05e7945c95eacbdad1dc6abe1400000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

3.	Prime Numbers (p,q): Used to compute n, but their approximate values are hinted:

Offset is calculated as offset= ((t−1)⋅10+g)/2 , where t is the turn number (15), and g is the group number (3).
Our goal was to decrypt the flag, encrypted using RSA, by exploiting the predictable nature of the prime numbers p and q.

## Breaking Down the Problem

We needed to:

1.	Implement a primality test to identify p and q.
2.	Derive the private key d using the Extended Euclidean Algorithm.
3.	Use d to decrypt the given ciphertext.

## Writing the Code

1. Primality Test (Miller-Rabin Algorithm)

We implemented the Miller-Rabin primality test to check if numbers close to the hinted primes were indeed prime. This test was chosen for its efficiency and reliability.

```python

def wit(a, d, n, r):
    x = pow(a, d, n)
    if x == 1 or x == n - 1:
        return False
    for _ in range(r - 1):
        x = pow(x, 2, n)
        if x == n - 1:
            return False
    return True

def random_miller(n, k):
    from random import randint
    for _ in range(k):
        a = randint(2, n - 2)
        if wit(a, n - 1, n, (n - 1).bit_length() - 1):
            return False
    return True

def miller(n, bases=None):
    if n <= 1:
        return False
    if n in (2, 3):
        return True
    if n % 2 == 0:
        return False
    
    r, d = 0, n - 1
    while d % 2 == 0:
        r += 1
        d //= 2

    if bases is None:
        if n < 2047:
            bases = [2]
        elif n < 1373653:
            bases = [2, 3]
        elif n < 25326001:
            bases = [2, 3, 5]
        elif n < 118670087467:
            bases = [2, 3, 5, 7, 11]
        elif n < 2152302898747:
            bases = [2, 3, 5, 7, 11, 13]
        else:
            return random_miller(n, k=10)

    for a in bases:
        if a >= n:
            break
        if not wit(a, d, n, r):
            return False
    return True

```

2. Finding p and q

Using the modulus n and the offset formula, we iterated over potential values for p and checked if q=n/p was also prime.

```python

def find_primes(n, offset, range_limit=100000000):
    t = 500 + offset
    p_guess = 2 ** t
    
    for dp in range(0, range_limit):
        p = p_guess + dp
        if miller(p):
            q = n // p
            if n % p == 0 and miller(q):
                return p, q
    raise ValueError("Primes not found.")

```

3. Calculating the Private Key d

Using the Extended Euclidean Algorithm, we computed the modular inverse of e mod (p−1)(q−1), giving us d, the private key.

```python

def inverse(e, phi):
    g, x, _ = gcd(e, phi)
    if g != 1:
        raise ValueError("modular inverse do not exist")
    return x % phi

def gcd(a, b):
    if b == 0:
        return a, 1, 0
    g, x1, y1 = gcd(b, a % b)
    x = y1
    y = x1 - (a // b) * y1
    return g, x, y

```

4. Decrypting the Ciphertext

With p, q, and d determined, we decrypted the ciphertext using the RSA formula m=cdmod  nm = c^d \mod nm=cdmodn.

```python

import binascii

def decrypt(ciphertext_hex, n, e, offset):
    p, q = find_primes(n, offset)
    
    phi = (p - 1) * (q - 1)
    d = inverse(e, phi)
    
    ciphertext = int.from_bytes(binascii.unhexlify(ciphertext_hex), "little")
    
    plaintext_int = pow(ciphertext, d, n)
    plaintext_bytes = plaintext_int.to_bytes((plaintext_int.bit_length() + 7) // 8, 'little')
    
    return plaintext_bytes.decode()

```


## Testing with the Given Data

We plugged in the provided values for n, e, and the ciphertext, and calculated the offset using the given t (15) and g (3).

```python

# Provided values
n = 119477202134466932562563268982823685174093386217657810644912885880702534757992106097239968629214788656866883802487322356532146500693429981738932845488692628293094673772453581148536651475011432263161744028861026196959738807441815153690687119150438113868535572665743017326891030515480059986743952870186268308272847161091535234421043628647351429721
e = 65537
ciphertext_hex = "6529642360750bd83ab84fa7d793fd0fedb4caa9b2e4118bb2032858012d69f206e507d8636f6aaec8569670bd70014e241ec3e96555fdbaa67c9adbd73d8a8c59c01d4d63ae5f70f7a6c9478d6daeb679520e62ae7f90995c03d6e5665cba81e92619063fc1215321034e37ac4304ad366433d5f27a60b1224c95df9d465b2ac2c05e7945c95eacbdad1dc6abe1400000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
g = 3
t = 15
offset = ((t - 1) * 10 + g) // 2

# Decrypt
text = decrypt(ciphertext_hex, n, e, offset)
print("Message:", text)
```

## Result

When we ran our script, it successfully decrypted the ciphertext, revealing the flag:
flag{ibufohqujsxcszgy}.

## Questions

### How did we use the information provided to infer the values used in the RSA encryption of the flag?

We used the formulas provided to estimate p and q. Then, we tested nearby numbers using the Miller-Rabin primality test to find the exact prime numbers. Finally, we verified that n=p⋅q to confirm their correctness.


### How did we confirm that our inference was correct?

After calculating p and q, we confirmed their correctness by:
- Verifying that p⋅q=n
- Ensuring that both p and q are prime using the Miller-Rabin primality test.

Both conditions were satisfied, confirming our values were correct.

### How did we extract the key from the ciphertext?

- Calculated ϕ(n):
ϕ(n)=(p−1)⋅(q−1)

- Found d:
Using the Extended Euclidean Algorithm, we computed d, the modular inverse of e mod  ϕ(n):

d=(e^(-1))mod  ϕ(n)

- Decrypted the ciphertext:

Converted the ciphertext from hexadecimal to a decimal number.
Applied the RSA decryption formula: m=(c^d)mod  n 

- Converted the message to text:

After obtaining m, we decoded it to retrieve the original message.

