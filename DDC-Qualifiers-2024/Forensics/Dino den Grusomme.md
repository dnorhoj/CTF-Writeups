# Dino den Grusomme

Proposed difficulty: `Hard`

5 solves

```text
Vi har anholdt en brandmand som vi mistænker er "Dino den Grusomme". Navnet har han fået grundet en række grusomme påsatte brande, og altid efterladt en dinsoaurbamse. Vi ved ikke med sikkerhed at det er ham, men vi fandt et lille drev gemt væk i hans lejlighed med nogle billeder af de tidligere brande på. Han har en mappe der hedder kommende brande, hvilket får politinæsen til at klø.

Kan du hjælpe med at finde ud af, om det faktisk kunne være ham, og hvor han har tænkt sig at starte den næste brand? Han er selv lidt en slacker, men går meget op i tal og orden, men han har venner der er ret gode til computerting, så vi ved ikke om han prøver at gemme noget for os.

Download drevet her
```

Attachments:

`Drev.e01: DOS/MBR boot sector, code offset 0x52+2, OEM-ID "NTFS    ", sectors/cluster 8, Media descriptor 0xf8, sectors/track 63, heads 255, hidden sectors 2048, dos < 4.0 BootSector (0), FAT (1Y bit by descriptor); NTFS, sectors/track 63, physical drive 0x80, sectors 4300799, $MFT start cluster 262144, $MFTMirror start cluster 2, bytes/RecordSegment 2^(-1*246), clusters/index block 1, serial number 07e56f55556f50ea1; contains bootstrap BOOTMGR`

# Solution

We get a dump of a drive. I primarily used TSK (The Sleuth Kit) to solve this challenge.

A quick `fls -r Drev.e01` shows us the following dinodes:

<details>

<summary>Output of fls</summary>

```text
r/r 4-128-1:	$AttrDef
r/r 8-128-2:	$BadClus
r/r 8-128-1:	$BadClus:$Bad
r/r 6-128-4:	$Bitmap
r/r 7-128-1:	$Boot
d/d 11-144-4:	$Extend
+ d/d 29-144-2:	$Deleted
+ r/r 25-144-2:	$ObjId:$O
+ r/r 24-144-3:	$Quota:$O
+ r/r 24-144-2:	$Quota:$Q
+ r/r 26-144-2:	$Reparse:$R
+ d/d 27-144-2:	$RmMetadata
++ r/r 28-128-4:	$Repair
++ r/r 28-128-2:	$Repair:$Config
++ d/d 31-144-2:	$Txf
++ d/d 30-144-2:	$TxfLog
+++ r/r 32-128-2:	$Tops
+++ r/r 32-128-4:	$Tops:$T
+++ r/r 33-128-1:	$TxfLog.blf
+++ r/r 34-128-1:	$TxfLogContainer00000000000000000001
+++ r/r 35-128-1:	$TxfLogContainer00000000000000000002
r/r 2-128-1:	$LogFile
r/r 0-128-6:	$MFT
r/r 1-128-1:	$MFTMirr
r/r 9-128-8:	$Secure:$SDS
r/r 9-144-11:	$Secure:$SDH
r/r 9-144-14:	$Secure:$SII
r/r 10-128-1:	$UpCase
r/r 10-128-4:	$UpCase:$Info
r/r 3-128-3:	$Volume
d/d 38-144-5:	Fede Brande
+ r/r 39-128-1:	010221.jpg
+ r/r 39-128-3:	010221.jpg:Zone.Identifier
+ r/r 40-128-1:	020222.jpg
+ r/r 40-128-3:	020222.jpg:Zone.Identifier
+ r/r 41-128-1:	030222.png
+ r/r 42-128-1:	030321.jpg
+ r/r 42-128-3:	030321.jpg:Zone.Identifier
+ r/r 43-128-1:	061021.jpg
+ r/r 43-128-3:	061021.jpg:Zone.Identifier
+ r/r 44-128-1:	070721.jpg
+ r/r 44-128-3:	070721.jpg:Zone.Identifier
+ r/r 45-128-1:	111121.jpg
+ r/r 45-128-3:	111121.jpg:Zone.Identifier
+ r/r 46-128-1:	150421.jpg
+ r/r 46-128-3:	150421.jpg:Zone.Identifier
+ r/r 47-128-1:	160422.jpg
+ r/r 47-128-3:	160422.jpg:Zone.Identifier
+ r/r 48-128-1:	220821.jpg
+ r/r 48-128-3:	220821.jpg:Zone.Identifier
+ r/r 49-128-1:	230921.jpg
+ r/r 49-128-3:	230921.jpg:Zone.Identifier
+ r/r 50-128-1:	241121.jpg
+ r/r 50-128-3:	241121.jpg:Zone.Identifier
+ r/r 51-128-1:	311221.jpg
+ r/r 51-128-3:	311221.jpg:Zone.Identifier
+ d/d 52-144-1:	Videoer
++ r/r 53-128-1:	010121.mp4
++ r/r 53-128-3:	010121.mp4:Zone.Identifier
++ r/r 54-128-1:	030121.mp4
++ r/r 54-128-3:	030121.mp4:Zone.Identifier
++ r/r 55-128-1:	060621.mp4
++ r/r 55-128-3:	060621.mp4:Zone.Identifier
d/d 56-144-1:	Kommende Brande
+ r/r 58-128-1:	plan_sneaky.secret
+ -/r * 59-128-1:	crypt.metadata
d/d 36-144-1:	System Volume Information
+ r/r 57-128-1:	IndexerVolumeGuid
+ r/r 37-128-1:	WPSettings.dat
V/V 256:	$OrphanFiles
```

</details>

## The goal

The most interesting files here are the file under the `Kommende Brande` directory:

`plan_sneaky.secret` - High entropy file suggesting that it is encrypted.

`crypt.metadata` - A deleted file that has the following contents:

```text
Encryption algorithm: AES 128
Mode: Cipher Block Chaining
IV: 4EDA35EE4C43B1581CAE5CC17A4ACF4F
Salt: No
Filetype: RTF
```

The goal seems to be to find the AES key, and decrypt the `plan_sneaky.secret` file.

## The slack space

We see that there are 13 images and 3 videos. After looking through these and trying to use stegsolve, stegseed and similar tools, I could not find anything interesting.

In total there are 16 files, which seems interesting, considering that we need a 128 bit aes key (16 bytes).

The challenge description hints towards him being a "slacker". After a bit of research, i found out that files can have more space allocated to them than is used in NTFS. This extra space is not used, and is called slack space.

I dumped the slack space by using the `icat` command from TSK:

`icat -s Drev.e01 <dinode> | tail -n 2 | xxd`

So for example, to dump the file called 030222.png's slack space, I used it's dinode (`41-128-1`):

`icat -s Drev.e01 41-128-1 | tail -n 2 | xxd`

This gave me the following output:

<details>

<summary>The output of the dump</summary>

```text
00000000: fef7 5c32 c8bb af03 4e99 86f1 86de 9405  ..\2....N.......
00000010: 5ff2 309c 2a3d d73d 971d 7df6 ec4f bb8c  _.0.*=.=..}..O..
00000020: c9a7 4266 da99 c73a 16e0 edff 156e a9d2  ..Bf...:.....n..
00000030: 8e1b f287 7a5e f803 7cc0 b1e0 e530 724f  ....z^..|....0rO
00000040: de48 1e74 3988 b73d 46e2 675e 51be c49b  .H.t9..=F.g^Q...
00000050: 658c 5134 0b37 7912 8f77 caa6 cddf 8c69  e.Q4.7y..w.....i
00000060: 5804 a2ee 431b 6bf9 397f 4e47 7245 fe9c  X...C.k.9.NGrE..
00000070: 9ce4 6adf 5bc5 67f3 d512 d7e2 4a26 7282  ..j.[.g.....J&r.
00000080: 5add 0bf5 e525 47a4 1bd3 77b1 d49d 971c  Z....%G...w.....
00000090: e94a 16a9 ad9e 533b a2b6 e6c3 d130 7edc  .J....S;.....0~.
000000a0: 1fc4 e07a 526d 8fda 0de1 c869 dbb6 ea2d  ...zRm.....i...-
000000b0: fd35 f041 def8 4a50 f104 f389 f002 3401  .5.A..JP......4.
000000c0: 77e8 0b0f a046 f951 38f2 5cfc 39d1 ce22  w....F.Q8.\.9.."
000000d0: 95eb 145a f44a cae7 ffba ba5f 0659 0a75  ...Z.J....._.Y.u
000000e0: 5d15 cb27 e17e 5a45 fcff 019f d13d 9a36  ]..'.~ZE.....=.6
000000f0: 603f 5900 0000 0049 454e 44ae 4260 8200  `?Y....IEND.B`..
00000100: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000110: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000120: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000130: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000140: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000150: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000160: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000170: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000180: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000190: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000200: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000210: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000220: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000230: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000240: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000250: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000260: 0000 0000 00b9 0000 0000 0000 0000 0000  ................
00000270: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000280: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000290: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000300: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000310: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000320: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000330: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000340: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000350: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000360: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000370: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000380: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000390: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000400: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000410: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000420: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000430: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000440: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000450: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000460: 0000 0000 0000                           ......
```

</details>

Keep in mind that this also dumps a bit of the actual file contents, but the slack space is primarily 0x00 bytes and a singular 0xb9 at relative offset 0x265.

This is the case for all the images and videos. The slack space contains a single byte that differs from 0x00. This is the AES key.

Here are the bytes for each file's slack space:

| Filename   | Byte |
| ---------- | ---- |
| 010221.jpg | 89   |
| 020222.jpg | 55   |
| 030222.png | b9   |
| 030321.jpg | b5   |
| 061021.jpg | 0b   |
| 070721.jpg | c9   |
| 111121.jpg | 84   |
| 150421.jpg | a4   |
| 160422.jpg | 9a   |
| 220821.jpg | 71   |
| 230921.jpg | c7   |
| 241121.jpg | 43   |
| 311221.jpg | d3   |
| 010121.mp4 | d9   |
| 030121.mp4 | af   |
| 060621.mp4 | 4c   |

## The AES key

We don't quite have a full AES key yet. We will need to find the correct order of the bytes. As all the files have filenames that are dates, we can use a python script to order the bytes by their corresponding filename dates.

```py
from datetime import datetime

data = [
    ["010221.jpg", 0x89],
    ["020222.jpg", 0x55],
    ["030222.png", 0xb9],
    ["030321.jpg", 0xb5],
    ["061021.jpg", 0x0b],
    ["070721.jpg", 0xc9],
    ["111121.jpg", 0x84],
    ["150421.jpg", 0xa4],
    ["160422.jpg", 0x9a],
    ["220821.jpg", 0x71],
    ["230921.jpg", 0xc7],
    ["241121.jpg", 0x43],
    ["311221.jpg", 0xd3],
    ["010121.mp4", 0xd9],
    ["030121.mp4", 0xaf],
    ["060621.mp4", 0x4c],
]

# Convert the first index of each element to a datetime object
for i in range(len(data)):
    date = data[i][0].split(".")[0]
    data[i][0] = datetime.strptime(date, "%d%m%y")

# Sort by date ascending
sorted_data = sorted(data, key=lambda x: x[0])

# Take the bytes from the sorted list
aes_key = bytes([i[1] for i in sorted_data])

print(f"AES key: {aes_key.hex()}")
```

From this script I get the following AES key:

`d9af89b5a44cc971c70b8443d355b99a`

## Decrypting the file

Now that we have an AES key, and the IV from `crypt.metadata`, we will use AES-CBC to decrypt the file, as described in `crypt.metadata`:

```py
from Crypto.Cipher import AES

key = bytes.fromhex("d9af89b5a44cc971c70b8443d355b99a")
iv = bytes.fromhex("4EDA35EE4C43B1581CAE5CC17A4ACF4F")

cipher = AES.new(key, AES.MODE_CBC, iv=iv)

with open("plan_sneaky.secret", "rb") as f:
    ct = f.read()

pt = cipher.decrypt(ct)

with open(b"plan_sneaky.decrypted", "wb") as f:
    f.write(pt)
```

We now have the decrypted file. Let's check the contents:

`$ head plan_sneaky.decrypted`

```text
{\rtf1\ansi\ansicpg1252\deff0\nouicompat\deflang1033{\fonttbl{\f0\fnil\fcharset0 Calibri;}}
{\colortbl ;\red0\green0\blue255;}
{\*\generator Riched20 10.0.22621}\viewkind4\uc1 
\pard\sa200\sl276\slmult1\b\f0\fs22\lang9 Plan Brandvarm\line\line\b0 Vi skal bruge: \par
8L diesel\line 4 T\'e6ndstikker\line En fjernstyret leget\'f8jskampvogn\line 2 s\'e6kke kartofler\line Og et Flag: DDC\{br4ndb4rm_b4l4d3\}\par
\par
{{\field{\*\fldinst{HYPERLINK https://www.youtube.com/watch?v=7JkrJUAg8aI }}{\fldrslt{https://www.youtube.com/watch?v=7JkrJUAg8aI\ul0\cf0}}}}\f0\fs22\par
\par
Og s\'e5 futter vi Vesterbro Brandstation af!\par
```

# Flag

`DDC{br4ndb4rm_b4l4d3}`
