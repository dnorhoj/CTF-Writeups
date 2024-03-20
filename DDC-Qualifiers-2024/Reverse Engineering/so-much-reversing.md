# so-much-reversing

Proposed difficulty: `Hard`

9 solves

```text
øhhhhhhhhhhhh vad?? "reversing"? altså "omvending"? tjaa den tror jeg godt jeg kan klare, du får flaget, men omvendt!!!!! er det en aftale? ok fister lagkage bare C med her du :U
```

Attachments:

```text
Archive:  chall.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
    17448  2023-11-23 22:05   jokes
      277  2023-11-23 22:28   log.txt
    16696  2023-11-23 22:05   reversed_flag
---------                     -------
    34421                     3 files
```

# Solution

Let's first look at `log.txt`. It has the following contents:

```bash
vim flag.c #reverse write my reverse flag
gcc flag.c -o flag #reverse compile my reverse flag 
gcc jokes.c -o jokes # leet compile my absolute hilarious c application
./jokes flag # this flag is such a joke and so funny 10hi, if you ever read this then tell the pope i said hi
```

This seems to show us how the jokes binary has been used, and it is probably in this step that the file called `reverse_flag` is created.

## Looking at the jokes binary

I started by loading up the jokes binary in ghidra.

After loading the program into ghidra I started looking into what it does. Here is the main function after some retyping and renaming:

<details>

<summary>The main() function</summary>

```c
int main(int argc,char **argv) {
  long lVar1;
  int choice;
  long in_FS_OFFSET;
  int i;
  char *jokes [12];
  char *argv_1;
  
  lVar1 = *(long *)(in_FS_OFFSET + 0x28);
  argv_1 = argv[1];
  i = 0;
  jokes[0] = "Why did the C script go to therapy?";
  jokes[1] = "Because it had serious \"array-rangement\" issues!";
  jokes[2] = (char *)0x0;
  jokes[3] = "Why did the C script break up with its sorting algorithm?";
  jokes[4] = 
  "Because every time they tried to patch things up, it just wanted to \"reverse\" the relationship! "
  ;
  jokes[5] = (char *)0x0;
  jokes[6] = "Why did the C script get a job as a chef?";
  jokes[7] = "Because it was an expert at flipping arrays in the kitchen!";
  jokes[8] = (char *)0x0;
  jokes[9] = "Why did the C script reverse the flag so many times?";
  jokes[10] = 
  "Because it wanted to... Duh.. And it was random as well.. I guess.. I don\'t know.. I\'m not omni scient.."
  ;
  jokes[11] = (char *)the_funniest_joke;
  while( true ) {
    choice = menu();
    if (choice != 1) break;
    tell_joke(jokes + (long)(i % 4) * 3,argv_1);
    i = i + 1;
  }
  puts(":(");
  if (lVar1 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

</details>

The program seems to do the following:

1. Initiate a bunch of different jokes, one of them being a pointer to a function.
2. Presents the user with a menu. If they select `1`, they will be told the next joke by running `tell_joke()`.
3. Then it prints a sad smiley and exits.

Let's look at the `tell_joke` function:

```c
long tell_joke(char **joke_ptr,char *argv_1) {
  puts(*joke_ptr);
  puts(joke_ptr[1]);
  if (joke_ptr[2] != (char *)0x0) {
    (*(code *)joke_ptr[2])(argv_1);
  }
  return 0;
}
```

It does the following:

1. It tells the joke.
2. If `joke[2]` is not equal to `0x0`, it will try to run it as a function.

As the fourth joke has a pointer to the function called `the_funniest_joke`, we will look into it and see what it does.

<details>

<summary>The the_funniest_joke() function</summary>

```c
int the_funniest_joke(char *file_path) {
  long lVar1;
  char *__ptr;
  ulong adjusted_bufsize;
  FILE *outfile_ptr;
  undefined *stack_buf_ptr;
  long in_FS_OFFSET;
  undefined stack_buf [8];
  char *file_path_copy;
  int buffer_size;
  int local_88;
  int i2;
  int i;
  int buffer_size_copy;
  int random_val_1;
  int random_val_2;
  char *file_buffer;
  long buffer_last_index;
  undefined *output_buffer;
  FILE *outfile_ptr_copy;
  char outfile_path [16];
  int i2_tmp;
  
  unique0x0000c300 = *(long *)(in_FS_OFFSET + 0x28);
  file_path_copy = file_path;
  file_buffer = open_file_from_args(file_path,&buffer_size);
  srand(0x539);
  buffer_size_copy = buffer_size;
  buffer_last_index = (long)buffer_size + -1;
  adjusted_bufsize = (((long)buffer_size + 15U) / 16) * 16;
  for (stack_buf_ptr = stack_buf;
      stack_buf_ptr != stack_buf + -(adjusted_bufsize & 0xfffffffffffff000);
      stack_buf_ptr = stack_buf_ptr + -0x1000) {
    *(undefined8 *)(stack_buf_ptr + -8) = *(undefined8 *)(stack_buf_ptr + -8);
  }
  lVar1 = -(ulong)((uint)adjusted_bufsize & 0xfff);
  if ((adjusted_bufsize & 0xfff) != 0) {
    *(undefined8 *)(stack_buf_ptr + ((ulong)((uint)adjusted_bufsize & 0xfff) - 8) + lVar1) =
         *(undefined8 *)(stack_buf_ptr + ((ulong)((uint)adjusted_bufsize & 0xfff) - 8) + lVar1);
  }
  output_buffer = stack_buf_ptr + lVar1;
  for (i = 0; i < 10000; i = i + 1) {
    *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 1053972;
    random_val_1 = rand();
    random_val_1 = random_val_1 % buffer_size_copy;
    *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 1053984;
    random_val_2 = rand();
    random_val_2 = random_val_2 % buffer_size_copy;
    if ((random_val_1 != random_val_2) && (i2_tmp = random_val_2, random_val_1 <= random_val_2)) {
      while (i2 = i2_tmp, local_88 = random_val_1, random_val_1 < i2) {
        output_buffer[random_val_2 - i2] = file_buffer[i2];
        i2_tmp = i2 + -1;
      }
      while (local_88 = local_88 + 1, local_88 < random_val_2) {
        file_buffer[local_88] = output_buffer[local_88 - random_val_1];
      }
    }
  }
  outfile_path[0] = 'r';
  outfile_path[1] = 'e';
  outfile_path[2] = 'v';
  outfile_path[3] = 'e';
  outfile_path[4] = 'r';
  outfile_path[5] = 's';
  outfile_path[6] = 'e';
  outfile_path[7] = 'd';
  outfile_path[8] = '_';
  outfile_path[9] = 'f';
  outfile_path[10] = 'l';
  outfile_path[11] = 'a';
  outfile_path[12] = 'g';
  outfile_path[13] = '\0';
  *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 0x1015e6;
  outfile_ptr = fopen(outfile_path,"wb");
  __ptr = file_buffer;
  outfile_ptr_copy = outfile_ptr;
  if (outfile_ptr != (FILE *)0x0) {
    *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 0x101637;
    fwrite(__ptr,(long)buffer_size,1,outfile_ptr);
    outfile_ptr = outfile_ptr_copy;
    *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 0x101643;
    fclose(outfile_ptr);
                    /* WARNING: Subroutine does not return */
    *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 0x10164d;
    exit(0);
  }
  *(undefined8 *)(stack_buf_ptr + lVar1 + -8) = 0x1015fd;
  perror("Error opening file");
  if (stack0xffffffffffffffc0 == *(long *)(in_FS_OFFSET + 0x28)) {
    return 0;
  }
                    /* WARNING: Subroutine does not return */
  __stack_chk_fail();
}
```

</details>

I was really tired when I originally looked at this challenge, and couldn't really be bothered with trying to understand what it did. But here is a quick overview:

1. It tries to read the file in `argv[1]` from disk.
2. It shuffles the contents of the file around.
   - This was the part I didn't look into at all
3. It creates a file called `reversed_flag` with the shuffled contents.

So it seems like this is the function that has shuffled the original binary which should give us the flag. So we have to undo this operation on the file `reversed_flag`.

As mentioned I was quite tired when looking at this, so I decided to monkeybrain it.

I did this by copying the code from `the_funniest_joke()`. And then applying the shuffle to an incremental long array with the same size as `reversed_flag`, instead of a char array as the program normally does. As the incremental array is shuffled in the same way, I can dump what the incremental array is turned into, and then use a quick python script to return the bytes from `reversed_flag` to their original locations.

**Warning: bad code ahead!**

<details>

<summary>Incremental array shuffler</summary>

```c
#include <stdio.h>
#include <stdlib.h>

#define FSIZE 16696

long *generate_incremental_array(int size) {
  long *arr = (long *)malloc(size * sizeof(long));
  for (int i = 0; i < size; i++) {
    arr[i] = i;
  }
  return arr;
}

long the_funniest_joke() {
  long lVar1;
  long *__ptr;
  unsigned long adjusted_bufsize;
  FILE *outfile_ptr;
  long *stack_buf_ptr;
  long in_FS_OFFSET;
  long stack_buf[8];
  int buffer_size;
  int cur_i;
  int i2;
  int i;
  int buffer_size_copy;
  int random_val_1;
  int random_val_2;
  long *file_buffer;
  long buffer_last_index;
  long *output_buffer;
  FILE *outfile_ptr_copy;
  char outfile_path[16];
  int dec_i;

  file_buffer = generate_incremental_array(FSIZE);
  buffer_size = FSIZE;
  srand(0x539);
  buffer_size_copy = buffer_size;
  buffer_last_index = (long)buffer_size + -1;
  adjusted_bufsize = (((long)buffer_size + 15U) / 16) * 16;
  for (stack_buf_ptr = stack_buf;
       stack_buf_ptr != stack_buf + -(adjusted_bufsize & 0xfffffffffffff000);
       stack_buf_ptr = stack_buf_ptr + -0x1000) {
    *(long *)(stack_buf_ptr + -8) = *(long *)(stack_buf_ptr + -8);
  }
  lVar1 = -(unsigned long)((unsigned int)adjusted_bufsize & 0xfff);
  if ((adjusted_bufsize & 0xfff) != 0) {
    *(long *)(stack_buf_ptr +
              ((unsigned long)((unsigned int)adjusted_bufsize & 0xfff) - 8) +
              lVar1) =
        *(long *)(stack_buf_ptr +
                  ((unsigned long)((unsigned int)adjusted_bufsize & 0xfff) -
                   8) +
                  lVar1);
  }
  output_buffer = stack_buf_ptr + lVar1;
  for (i = 0; i < 10000; i = i + 1) {
    *(long *)(stack_buf_ptr + lVar1 + -8) = 0x101514;
    random_val_1 = rand();
    random_val_1 = random_val_1 % buffer_size_copy;
    *(long *)(stack_buf_ptr + lVar1 + -8) = 1053984;
    random_val_2 = rand();
    random_val_2 = random_val_2 % buffer_size_copy;
    if ((random_val_1 != random_val_2) &&
        (dec_i = random_val_2, random_val_1 <= random_val_2)) {
      while (i2 = dec_i, cur_i = random_val_1, random_val_1 < i2) {
        output_buffer[random_val_2 - i2] = file_buffer[i2];
        dec_i = i2 + -1;
      }
      while (cur_i = cur_i + 1, cur_i < random_val_2) {
        file_buffer[cur_i] = output_buffer[cur_i - random_val_1];
      }
    }
  }

  // Dump new order to stdout
  for (int i = 0; i < FSIZE; i++) {
    printf("%ld\n", file_buffer[i]);
  }
}

int main() {
  the_funniest_joke();
  return 0;
}
```

</details>

By compiling this file, and running it, I get a line for each number, in the shuffled order.

It looks something like this:

```sh
$ gcc -o solve_monkey{,.c}
$ ./solve_monkey | head -n 20
0
1
2
3
4
15730
3447
569
11466
11467
1803
3107
3106
15418
15419
11728
11727
11726
11725
11724
```

I dumped this list into a file called `order.txt`, and ran the following python script to recover the original file:

```py
with open('reversed_flag', 'rb') as f:
    shuffled = f.read()

with open('order.txt', 'r') as f:
    order = list(map(int, f.read().splitlines()))

flag = [0] * len(shuffled)

for i, o in enumerate(order):
    flag[o] = shuffled[i]

with open('flag', 'wb') as f:
    f.write(bytes(flag))
```

And sure enough:

```sh
$ python fix.py
$ file flag
flag: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=d77a31a0b4fb356da3d6852b1bd0eb2b58eb719b, for GNU/Linux 3.2.0, not stripped
$ chmod +x flag            
$ docker run --rm -v "$(pwd):/work" -it ubuntu /work/flag    
}n14g4_t1_35r3v3r_3w_f1_t4hw{CDD
$ docker run --rm -v "$(pwd):/work" -it ubuntu /work/flag | rev
DDC{wh4t_1f_w3_r3v3r53_1t_4g41n}
```

It's not beautiful but it works 😅.

# Flag

`DDC{wh4t_1f_w3_r3v3r53_1t_4g41n}`
