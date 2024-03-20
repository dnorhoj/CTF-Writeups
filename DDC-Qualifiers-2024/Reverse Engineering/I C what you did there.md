# I C what you did there

Proposed difficulty: `Easy`

33 solves

```text
En eller anden går rundt og vil spille det der spil med kopper hvor man skal gætte hvor bolden er henne. Der er dog ingen der har besejret ham endnu. Kan du vinde spillet?

Du kan begynde spillet at ved bruge kommandoen "nc shuffler.hkn 1337" source
```

Attachments:

```text
Archive:  shuffleGame.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
       25  2023-12-01 23:18   flag.txt
    16416  2023-12-02 02:52   shuffler
---------                     -------
    16441                     2 files
```

## Solution

Let's start by loading `shuffler` into ghidra.

We will start by looking into what the `main()` function does:

```c
void main(void) {
  long lVar1;
  int iVar2;
  FILE *__stream;
  long in_FS_OFFSET;
  char flagchar;
  char input [64];
  char local_48 [32];
  
  lVar1 = *(long *)(in_FS_OFFSET + 0x28);
  local_48[0] = 'T';
  local_48[1] = 'h';
  local_48[2] = 'e';
  local_48[3] = 'C';
  local_48[4] = 'u';
  local_48[5] = 'p';
  local_48[6] = 's';
  local_48[7] = 'G';
  local_48[8] = 'o';
  local_48[9] = 'R';
  local_48[10] = 'o';
  local_48[11] = 'u';
  local_48[12] = 'n';
  local_48[13] = 'd';
  local_48[14] = '&';
  local_48[15] = 'R';
  local_48[16] = 'o';
  local_48[17] = 'u';
  local_48[18] = 'n';
  local_48[19] = 'd';
  local_48[20] = 'I';
  local_48[21] = 'n';
  local_48[22] = 'C';
  local_48[23] = 'i';
  local_48[24] = 'r';
  local_48[25] = 'c';
  local_48[26] = 'l';
  local_48[27] = 'e';
  local_48[28] = 's';
  local_48[29] = '\0';
  local_48[30] = '\0';
  local_48[31] = '\0';
  fgets(input,0x32,stdin);
  shuffler(input);
  iVar2 = checkInput(input,local_48);
  if (iVar2 == 1) {
    __stream = fopen("flag.txt","r");
    iVar2 = fgetc(__stream);
    flagchar = (char)iVar2;
    while (flagchar != -1) {
      putchar((int)flagchar);
      iVar2 = fgetc(__stream);
      flagchar = (char)iVar2;
    }
    fclose(__stream);
  }
  else {
    puts("Incorrect input");
  }
  if (lVar1 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}
```

It seems like it does the following:

1. Initializes a buffer to contain the string `TheCupsGoRound&RoundInCircles\x00\x00\x00`
2. It takes 32 bytes of user input from stdin
3. It runs the function `shuffler()` on our input
4. It runs `checkInput()` on the shuffled input, as well as the buffer that was previously initialized.

Let's start by looking at checkInput, to see what is considered a correct input:

```c
int checkInput(char *input, char *solution) {
  int i;
  
  i = 0;
  while(28 < i) {

    if (input[i + 1] != solution[i]) {
      return 0;
    }

    i = i + 1;
  }

  return 1;
}
```

It seems like we will have to get at least the first 29 characters right, and we will win.

Let's look at `shuffler()`:

```c
char *shuffler(char *input) {
  long in_FS_OFFSET;
  int i;
  int i2;
  int i3;
  char buf [56];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  for (i = 0; i < 50; i = i + 1) {
    buf[i] = input[i];
  }
  for (i2 = 0; i2 < 12; i2 = i2 + 1) {
    buf[i2] = input[0x1d - i2];
    buf[0x1d - i2] = input[i2];
  }
  for (i3 = 0; i3 < 31; i3 = i3 + 1) {
    input[i3] = buf[i3];
  }
  printf("%s",input);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return input;
}
```

I rewrote it to a more simple python script such that I could play around with it:

```py
from pwn import *

def shuffler(input_str):
    # Create a buffer list with 56 elements
    buf = [''] * 56

    # First loop: Copy first 50 characters from input to buf
    for i in range(min(len(input_str), 50)):
        buf[i] = input_str[i]

    # Second loop: Swap characters in buf
    for i2 in range(12):
        buf[i2], buf[29 - i2] = input_str[29 - i2], input_str[i2]

    # Third loop: Copy back first 31 characters to input
    input_str = ''.join(buf[i3] for i3 in range(31))

    return input_str

data = " TheCupsGoRound&RoundInCircles  "
shuffled = shuffler(list(data))

#io = process("./shuffler")
io = remote("shuffler.hkn", 1337)

io.sendline(shuffled.encode())

io.recvline()

print(io.recvall().decode())
```

It didn't work until i put a space at the start of the input string. But after I added it I got the following output:

```bash
$ python shuffler.py
[+] Opening connection to 77.143.64.228 on port 1337: Done
[+] Receiving all data: Done (1.02KB)
[*] Closed connection to 77.143.64.228 port 1337
/  \    /  \ ____ |  |   ____  ____   _____   ____  
\   \/\/   // __ \|  | _/ ___\/  _ \ /     \_/ __ \ 
 \        /\  ___/|  |_\  \__(  <_> )  Y Y  \  ___/ 
  \__/\__/  \___ > ____/\___/ \____/|__|_|  /\___ >

.____           __           ___.                 .__        
|    |    _____/  |_  ______ \_ |__   ____   ____ |__| ____  
|    |  _/ __ \   __\/  ___/  | __ \_/ __ \ / ___\|  |/    \ 
|    |__\  ___/|  |  \___ \   | \_\ \  ___// /_/  >  |   |  \ 
|_______ \___  >__| /____  >  |___  /\___  >___  /|__|___|  /
        \/   \/          \/       \/     \/_____/         \/ 
   __  __                                                      
  / /_/ /_  ___     _______  ______     ____ _____ _____ ___  ___ 
 / __/ __ \/ _ \   / ___/ / / / __ \   / __  / __  / __  __ \/ _ \ 
/ /_/ / / /  __/  / /__/ /_/ / /_/ /  / /_/ / /_/ / / / / / /  __/
\__/_/ /_/\___/   \___/\__ _/  ___/   \__  /\__ _/_/ /_/ /_/\___/ 
                           /_/       /____/
Give me an input: 
 TheCupsGoRound&RoundInCircles 
DDC{K33p_my_3y3s_0n_th3_priz3}
```

# Flag

`DDC{K33p_my_3y3s_0n_th3_priz3}`
