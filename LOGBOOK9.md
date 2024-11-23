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