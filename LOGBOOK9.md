# Secret Key Encryption

## Task 1

For this lab, we were given a cipher-text that is encrypted using a monoalphabetic cipher, where each letter in the original text is replaced by another letter and the replacement will not vary. To decifer the text, we will be performing a frequency analysis.
We ran the 'freq.py' script in order to get the statistics for n-grams:

```
$seed@VM:~/.../Files$ python3 freq.py 
-------------------------------------
1-gram (top 20):
n: 488
y: 373
v: 348
x: 291
u: 280
q: 276
m: 264
h: 235
t: 183
i: 166
p: 156
a: 116
c: 104
z: 95
l: 90
g: 83
b: 83
r: 82
e: 76
d: 59
-------------------------------------
2-gram (top 20):
yt: 115
tn: 89
mu: 74
nh: 58
vh: 57
hn: 57
vu: 56
nq: 53
xu: 52
up: 46
xh: 45
yn: 44
np: 44
vy: 44
nu: 42
qy: 39
vq: 33
vi: 32
gn: 32
av: 31
-------------------------------------
3-gram (top 20):
ytn: 78
vup: 30
mur: 20
ynh: 18
xzy: 16
mxu: 14
gnq: 14
ytv: 13
nqy: 13
vii: 13
bxh: 13
lvq: 12
nuy: 12
vyn: 12
uvy: 11
lmu: 11
nvh: 11
cmu: 11
tmq: 10
vhp: 10

```
After that, we read the provided wikipedia articles. We found out that "th" is the most common bigram in the English language and that "the" is the most common trigram. Since the most frequent 2-gram and 3-gram in our text is, respectively, "yt" and "ytn", we concluded that they corresponded to "th" and "the". We started the substitution:

```
$ tr "ytn" "THE" < ciphertext.txt > test.txt
```
After that, we noticed that "and" is the second most common 3-gram,"vup" is the second most frequent 3-gram in the text and "v" is the third most frequent leter, so it might be "a", and "an" is also a very frequent 2-gram. So we proceeded to the substitution:
```
$ tr "ytnvup" "THEAND" < ciphertext.txt > test.txt
```
Then we noticed that the third most common 2-gram is "in" and it might be "mu" in our text, as "i" is also one of the most frequent 1-grams, so it is plausible:
```
$ tr "ytnvupm" "THEANDI" < ciphertext.txt > test.txt
```
Then, we went for the fourth most common 2-gram, which is "er", in our case its "nh", which is also plausible as "n" is the most common 1-gram and it corresponds to "e":
```
$ tr "ytnvupmh" "THEANDIR" < ciphertext.txt > test.txt
```
Finally, we opened up 'test.txt' and were able to fill out some words, whose missing letters were very obvious(eg.: "RIrHT AbTER" "rb"->"gf"):
```
$ tr "ytnvupmhrb" "THEANDIRGF" < ciphertext.txt > test.txt
```
We did that for the rest of the letters, until we got the complete decryption:
```
$ tr "ytnvupmhrbqlsxiazdegcjfok" "THEANDIRGFSWKOLCUYPBMQVJX" < ciphertext.txt > test.txt
```
As we can see, only one letter is missing: "w" in the decryption, which translates to "Z" (not present in the text). <br>
In conclusion, monoalphabetic ciphers are vulnerable to frequency analysis because the letter frequency patterns in the ciphertext remain consistent with the original text. So, it's possible to decrypt the message and break the cipher by identifying common letter pairs and applying systematic substitutions.

## Task 2 

In this task, we used the `openssl` command, in order to encrypt and decrypt files with various cyphers and modes. By opening the manual of openssl and enc (by using `man`), we can see that it supports various encryption algorithms.
We generated a new file, with 1500 bytes (`plaintext.txt`), to test the different cyphers and modes.
We tested different cyphers:
```
$ openssl enc -aes-128-ecb -e -in plaintext.txt -out cipher.bin \
-K 00112233445566778889aabbccddeeff
```
```
$ openssl enc -aes-128-cbc -e -in plaintext.txt -out cipher.bin \
-K 00112233445566778889aabbccddeeff \
-iv 0102030405060708

hex string is too short, padding with zero bytes to length
```
```
$ openssl enc -aes-128-ctr -e -in plaintext.txt -out cipher.bin \
-K 00112233445566778889aabbccddeeff \
-iv 0102030405060708

hex string is too short, padding with zero bytes to length
```

### Question 1

To perform the encryptions, we had to add some flags for the encryption:

- e: Indicates encryption
- in <file>: Input file
- out <file>: Output file
- K <key>: encryption key in hexadecimal (128 bits, 16 bytes)
- iv <iv>: Initialization Vector (128 bits, 16 bytes). Required for CBC and CTR, but not for ECB.

Differences Between Modes:
* ECB (Electronic Codebook): Encrypts each block independently and identical plaintexts with identical keys produce identical ciphertexts. Multiple encryptions can be made in parallel, but, because indentical plaintexts/keys produce identical ciphertexts, it is insecure for structured or repetitive data.

* CBC (Cipher Block Chaining): Chains blocks together by 'XORing' each plaintext block with the previous ciphertext block. Requires an IV (-iv flag) for the first block. CBC is secure, but it cannot be encrypted in parallel because each block depends on the previous one.

* CTR (Counter):  In Counter mode, AES works by encrypting an incrementing counter. The encrypted counter is then 'XORed' with the plaintext to produce ciphertext. Uses the IV (-iv flag) as a counter, which is incremented for each block. It allows parallel encryption and decryption and is secure as long as the the counter isn't reused with the same key.

In conclusion, ECB is the simplest mode but insecure for repetitive data due to identical ciphertexts for identical plaintexts. CBC improves security by chaining blocks together, but it cannot be parallelized, because it's dependant on previous ciphertexts. On the other hand, CTR can be encrypted in parallel by using an incrementing counter.


We then decrypted `cipher.bin` into a text file ('decrypt.txt'), using the modes we previously tested.


### Question 2

To decypher, we used the previous commands, with the input (flag -in) as 'cipher.bin' and the output (flag -out) as 'decrypt.txt', with the flag -d instead of -e, which indicates that we will be decrypting the input file.
The main difference between aes-128-ctr and the other modes is that in CTR mode, decryption is identical to encryption. This is because the encrypted counter values are XORed with the ciphertext to recover the plaintext, just as they were 'XORed' with the plaintext during encryption. Unlike CBC, CTR does not depend on the previous ciphertext block, allowing parallel decryption, but making it safer than EBC, because the counter ensures each block is processed uniquely, even if the plaintext has repetitive patterns.


## Task 5

We started by encrypting `plaintext.txt` using the previous modes:

```
$ openssl enc -aes-128-ecb -e -in plaintext.txt -out cipher_ecb.bin \
-K 00112233445566778889aabbccddeeff
```
```
$ openssl enc -aes-128-cbc -e -in plaintext.txt -out cipher_cbc.bin \
-K 00112233445566778889aabbccddeeff \
-iv 0102030405060708

hex string is too short, padding with zero bytes to length
```
```
$ openssl enc -aes-128-ctr -e -in plaintext.txt -out cipher_ctr.bin \
-K 00112233445566778889aabbccddeeff \
-iv 0102030405060708

hex string is too short, padding with zero bytes to length
```

We then changed the byte 150 (50* group_num 3) of each `.bin` file, using the bless editor.

![before](/docs/images/lb_9.jpg)
![after](/docs/images/lb_9_2.jpg)

After the changes, we decrypted the files:

```
$ openssl enc -aes-128-ecb -d -in cipher_ecb.bin -out decrypt_ecb.txt \
-K 00112233445566778889aabbccddeeff
```
```
$ openssl enc -aes-128-cbc -d -in cipher_cbc.bin -out decrypt_cbc.txt \
-K 00112233445566778889aabbccddeeff \
-iv 0102030405060708

hex string is too short, padding with zero bytes to length
```
```
$ openssl enc -aes-128-ctr -d -in cipher_ctr.bin -out decrypt_ctr.txt \
-K 00112233445566778889aabbccddeeff \
-iv 0102030405060708

hex string is too short, padding with zero bytes to length
```

We predicted that the following would happen for the different modes:

* ECB Mode:
Since ECB mode encrypts each block independently, altering one byte of the ciphertext will affect the entire 16-byte block during decryption.
Expected Outcome: 16 bytes of information will be corrupted.

* CBC Mode:
In CBC mode, the ciphertext is chained, meaning the corruption of one byte in a ciphertext block will affect both the corrupted block and the next block.
Expected Outcome: 32 bytes of information will be corrupted (16 bytes from the corrupted block and 16 bytes from the next block).

* CTR Mode:
In CTR mode, altering one byte in the ciphertext only affects that corresponding byte in the decrypted plaintext. This is because each byte is encrypted independently using the 'XOR' of the plaintext and the encrypted counter.
Expected Outcome: Only 1 byte of information will be corrupted.



We then opened each text file to see the output, we verified that:

* When one byte of the ciphertext is altered in ECB mode, 16 bytes of information are affected.
In ECB, each block of plaintext is encrypted independently, and the same plaintext produces the same ciphertext.
The ciphertext is divided into fixed blocks (128 bits, 16 bytes). Corrupting one byte in the ciphertext causes the entire block to become corrupted during decryption.

![ecb](/docs/images/lb9_ecb.jpg)

* When one byte of the ciphertext is corrupted in CBC mode, more than 16 bytes of information are corrupted.
In CBC, each ciphertext block depends on the previous ciphertext block (due to the XOR operation). Corrupting a byte in one ciphertext block affects both the block that was corrupted and the next block.
The corrupted ciphertext will affect the decryption of the current block entirely. Additionally, the following block will also be corrupted because the XOR with the previous (now changed) ciphertext affects the decryption of the next block.
As a result, 32 bytes (16 bytes from the current block and 16 bytes from the next block) will be corrupted.

![cbc](/docs/images/lb9_cbc.jpg)

* When one byte of the ciphertext is altered in CTR mode, only 1 byte of information is lost.
In CTR, the ciphertext is generated by XORing the plaintext with an encrypted counter value. The counter is incremented for each block, and no block is dependent on previous ones. The rest of the block was not compromised, because each byte of ciphertext is independently generated by the XOR of the plaintext byte and the corresponding encrypted counter value.

![ctr](/docs/images/lb9_ctr.jpg)




