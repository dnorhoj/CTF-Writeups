# Irriterende Kollegaer

Proposed difficulty: `Hard`

8 solves

```text
Jeg mistænker min kollega for at have gjort noget ved min computer. Jeg sad ved min maskine, og pludselig kom der nogle mærkelig beskeder op, og samtidig så jeg et USB stik i min computer! Jeg skyndte mig at fjerne USB stikket, og så slukkede computeren. Jeg må ikke tænde den for min chef, før I har undersøgt det.

Vi kan ikke få adgang til selve disken, men heldigvis har IT en agent der laver memorybackup ret ofte. IT sagde dog at jeg måske trak USB stikket lidt hurtigt ud, men det ikke burde gøre det store.
```

Attachments:

`mem.vmem: Windows Event Trace Log` (Incorrectly assumed to be an ETL although it is a memory dump)
`usb4.img: DOS/MBR boot sector, code offset 0x52+2, OEM-ID "NTFS    ", sectors/cluster 8, Media descriptor 0xf8, sectors/track 63, heads 255, hidden sectors 2048, dos < 4.0 BootSector (0), FAT (1Y bit by descriptor); NTFS, sectors/track 63, physical drive 0x80, sectors 3071999, $MFT start cluster 128000, $MFTMirror start cluster 2, bytes/RecordSegment 2^(-1*246), clusters/index block 1, serial number 0e4a6d46ca6d4412a; contains bootstrap BOOTMGR`

# Solution

We have two files. The first is a Windows memory dump, and the other is an NTFS drive.

The NTFS drive has a lot of `.exe` files in it. This is interesting although not very useful at the moment.

## Examining the memory dump

Let's take a look at the memory dump. As the NTFS drive has a lot of .exe files in it, I will start by dumping the active processes from the memory dump.
For this purpose I will make use of volatility 3.

`$ vol -f mem.vmem windows.pslist`

Here a line stood out in particular:

`4156    3508    rainbow.exe     0x908d1cdd4080  4       -       1       False   2023-11-29 18:06:04.000000      N/A     Disabled`

The process `rainbow.exe` seems to be the only program running under WoW64.

It happens that `rainbow.exe` is also in the NTFS drive.

## `rainbow.exe`

I started by loading `rainbow.exe` in ghidra. After a bit of renaming and retyping I got the following `main()` function:

<details>

<summary>Decompiled main() function</summary>

```c
int __cdecl main(int _Argc,char **_Argv,char **_Env) {
  u_short uVar1;
  int iVar2;
  uint errcode;
  FILE *_File;
  char *source;
  undefined8 local_600;
  WSADATA local_5f8;
  char recvbuf [1036];
  int local_4c;
  undefined local_48 [16];
  sockaddr sockaddr;
  int local_24;
  SOCKET socket;
  
  __main();
  pthread_create(&local_600,(uint *)0x0,&heartbeat,0);
  Sleep(5000);
  local_4c = 0x10;
  printf("\nInitialising Winsock...");
  iVar2 = WSAStartup(0x202,&local_5f8);
  if (iVar2 != 0) {
    errcode = WSAGetLastError();
    printf("Failed. Error Code : %d",(ulonglong)errcode);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  printf("Initialised.\n");
  socket = ::socket(2,2,0);
  if (socket == 0xffffffffffffffff) {
    errcode = WSAGetLastError();
    printf("Could not create socket : %d",(ulonglong)errcode);
  }
  printf("Socket created.\n");
  sockaddr.sa_family = 2;
  sockaddr.sa_data[2] = '\0';
  sockaddr.sa_data[3] = '\0';
  sockaddr.sa_data[4] = '\0';
  sockaddr.sa_data[5] = '\0';
  sockaddr.sa_data._0_2_ = htons(1337);
  iVar2 = bind(socket,&sockaddr,0x10);
  if (iVar2 == -1) {
    errcode = WSAGetLastError();
    printf("Bind failed with error code : %d",(ulonglong)errcode);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  puts("Bind done");
  while( true ) {
    printf("Waiting for data...");
    _File = (FILE *)(*(code *)__imp___acrt_iob_func)(1);
    fflush(_File);
    memset(recvbuf,0,0x400);
    local_24 = recvfrom(socket,recvbuf,0x400,0,(sockaddr *)local_48,&local_4c);
    if (local_24 == -1) break;
    uVar1 = ntohs(local_48._2_2_);
    source = inet_ntoa((in_addr)local_48._4_4_);
    printf("Received packet from %s:%d\n",source,(ulonglong)uVar1);
    printf("Data: %s\n",recvbuf);
    if (recvbuf[0] == '\x0f') {
      system("E:\\netbtugc.exe");
    }
  }
  errcode = WSAGetLastError();
  printf("recvfrom() failed with error code : %d",(ulonglong)errcode);
                    /* WARNING: Subroutine does not return */
  exit(1);
}
```

</details>

In rough terms this program does the following:

1. Binds a TCP listener to port `1337`
2. When a connection occurs and the data starts with `\x0f` it runs the program `E:\netbtugc.exe`.
3. Something happens inside `netbtugc.exe`

This seems to be the right direction as it seems to be some sort of C2.

## `netbtugc.exe`

I started by loading `netbtugc.exe` in ghidra. After a bit of renaming and retyping I got the following `main()` function:

<details>

<summary>Decompiled main() function</summary>

```c
int __cdecl main(int _Argc,char **_Argv,char **_Env) {
  int compare_result;
  size_t iplength;
  undefined argument_pt2 [3];
  undefined local_145;
  undefined8 argument_pt1;
  undefined8 local_120;
  undefined funny_data [4];
  undefined local_104;
  undefined uStack_103;
  undefined uStack_102;
  undefined uStack_101;
  undefined4 uStack_100;
  undefined8 local_fc;
  undefined local_f0 [2];
  undefined local_ee;
  char command [112];
  char *local_28;
  char *runner_ip;
  char *special_ip;
  char *binary;
  
  __main();
  binary = "shutdown /s /t 5 /c ";
  memcpy(funny_data,"xeff2xC|57up1dc0w0rkccre3r00xfer",0x20);
  local_104 = 0x44;
  uStack_103 = 0x44;
  uStack_101 = 0x7b;
  memset(&argument_pt1,0,0x1e);
  argument_pt1 = CONCAT44(uStack_100,
                          CONCAT13(uStack_101,CONCAT12(uStack_102,CONCAT11(uStack_103,iVar1%s \"%s%s\"",binary,&argument_pt1,argument_pt2);
  special_ip = "192.168.111.128";
  runner_ip = getIPAddress();
  iplength = strlen(special_ip);
  compare_result = memcmp(special_ip,runner_ip,iplength);
  if (compare_result != 0) {
    local_28 = "shutdown /s /t 5 /c \"Go away\"";
    system("shutdown /s /t 5 /c \"Go away\"");
                    /* WARNING: Subroutine does not return */
    exit(-1);
  }
  system(command);
  return 0;
}
```

</details>

In rough terms, this is what the program does:

1. Scrambles some text arund to create a system command
2. Checks if the IP of the computer running the script is equal to `192.168.111.128`
   - This is probably checked as the "irritating colleague" does not want their own computer to be turned off if this script is run on their computer.
3. If the script is not equal to `192.168.111.128`, it will turn off the pc
4. If the script is equal to `192.168.111.128`, it will run the command that was previously generated by shuffling stuff around in a low-level manner.

So it seems like the goal is to either run this while having the IP address `192.168.111.128`, or trick the program into thinking it is.

As I didn't have a windows computer at hand, I didn't really have a way to run a debugger such as WinDBG. Instead I opted for a more unconventional approach, by patching the if statement at the end to be a `JNZ` (Jump if not zero) instead of a `JZ` (Jump if zero), which inverts the jump.

I did this by locating the the `JZ` instruction which is at offset `0xE85`. The `JZ` instruction is denoted by the byte `0x74`. By changing this to a `0x75` which is the opcode for `JNZ`.

By running the patched file in wine, I got the following output:

```bash
$ wine netbtugc.exe
019c:fixme:shutdown:wmain stub: L"shutdown" L"/s" L"/t" L"5" L"/c" L"DDC{57up1dc0w0rk3r}"
```

# Flag

`DDC{57up1dc0w0rk3r}`