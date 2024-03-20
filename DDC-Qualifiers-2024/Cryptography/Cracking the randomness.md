# Cracking the randomness

Proposed difficulty: `Medium`

31 solves

```text
Tilfældige tal bruges hele tiden! Men hvor tilfældige er de egentlig?

Til denne udfordring kan det være en fordel at søge rundt efter information og værktøjer til at angribe pythons random funktion!
```

Attachments:

```text
Archive:  challenge.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
     6803  2024-02-21 00:00   output.txt
      383  2024-02-20 23:50   main.py
       13  2024-02-20 23:50   flag.txt
---------                     -------
     7199                     3 files
```

# Solution

`main.py` has the following contents:

```py
import random

# How random is random 0.0

with open("flag.txt", "rb") as f:
    Flag = f.read()

with open("output.txt", "w") as f:
    for i in range(624):
        f.write(f'{random.getrandbits(32)}\n')

    while Flag:
        FlagPre, Flag = Flag[:4], Flag[4:]
        otp = random.getrandbits(32)
        res = int.from_bytes(FlagPre,"big")
        f.write(f'{res^otp}\n')
    
```

In this challenge we exploit the python `random` PRNG module.

I must admit that i don't have a lot of knowledge of how this works internally, but i made use of the python library called `randcrack` that takes 624 32 bit numbers from the random library, and can synchronize itself with the random state. From here we can decrypt the flag:

```py
from randcrack import RandCrack
from Crypto.Util.number import long_to_bytes

rc = RandCrack()

with open("output.txt", "r") as f:
    for i in range(624):
        rc.submit(int(f.readline()))

    flag = b''
    for i in range(20):
        line = f.readline()

        if not line:
            break

        num = rc.predict_getrandbits(32)
        actualnum = int(line)

        flag += long_to_bytes(num ^ actualnum)

    print(flag.decode().strip())
```

# Flag

`DDC{Not_so_random_anymore_mr_randcrack}`
