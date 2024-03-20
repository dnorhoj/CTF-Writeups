# Affinity

Proposed difficulty: `Medium`

58 solves

```text
Et affine cipher krypterer en klartekst x til chiffertekst y ved at beregne y = (ax + b) mod m.

Jeg har designet denne sikre krypteringsmekanisme ved hjælp af et affine cipher. Kan du bryde den?
```

Attached files:

```text
Archive:  affinity.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
      760  2024-02-04 10:07   gen.py
       74  2024-02-04 10:07   output.txt
---------                     -------
      834                     2 files
```

# Solution

The encryption works by taking the variables `a`, `b` and `m`, and assuming the plaintext is ASCII, does the following calculation for each byte:

$$c = a \cdot letter + b\ (\text{mod}\ m)$$

Furthermore we know that $gcd(a,m) = 1$ and $gcd(b,m) = 1$. Which means that we can use the multiplicative inverse of a and b to reverse the operation.

We can reverse this by doing the following calculation for each byte.

$$letter = a^{-1} \cdot (c - b)\ (\text{mod}\ m)$$

This works as we assume that the plaintext is ASCII, which means that $0 \leq letter \leq 256$.

$m$ is hardcoded to be $256$. While $a$ and $b$ are unknown, we know that $0 \leq b \leq 256$ and $1 \leq a \leq 256$. Therefore there are at most $256*257$ possible values of $a$ and $b$, which is easily bruteforceable with the following script:

```py
from math import gcd
import random
import binascii

def affine_decrypt(ciphertext, a, b, m):
    result = ''
    while ciphertext:
        c = ciphertext & 0xff
        result = chr((pow(a, -1, m) * (c - b)) % m) + result
        ciphertext >>= 8
    return result

# Output taken from output.txt
output = "0xe9e962ea422bdc1d39965e2b341de5dc260f5050c9b2260f960f016c0f50342b6c39dcf8"
output = int(output, 16)

# Decrypt the flag
m = 256
for a in range(1, m):
    if gcd(a, m) != 1:
        continue
    for b in range(m+1):
        try:
            x = affine_decrypt(output, a, b, m)

            if "DDC" in x:
                print(x)
        except:
            pass
```

# Flag

`DDC{cryptography_needs_nonlinearity}`
