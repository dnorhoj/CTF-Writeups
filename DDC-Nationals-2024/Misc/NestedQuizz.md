# NestedQuizz

Proposed difficulty: `Easy`

19 solves - 166 points

```text
Du ønskede virkelig et job hos et bestemt cybersikkerhedsfirma, men det krævede 7 forskellige ansøgninger til forskellige stillinger, før de endelig svarede. De gav dig en udfordring, som vil give dig jobinterviewet, hvis du løser den.

OBS: Alle svar/passwords er ét ord med stort begyndelsesbogstav, fx Password.
```

Attachments:

```text
Archive:  misc-nestedquizz.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
    51490  2024-03-31 16:36   JKVhwx.igz
  1376275  2024-04-08 18:58   Level1.rar
---------                     -------
  1427765                     2 files
```

# Solution

This challenge is split into multiple levels.

# Level 1

To start off we have the following files:

- JKVhwx.igz
- Level1.rar

The challenge is in the .igz file, and the rar file is protected by a password.

Let's check the filetype of the JKVhwx.igz file with the `file` command:

```sh
$ file JKVhwx.igz
JKVhwx.igz: PNG image data, 1015 x 1015, 8-bit/color RGBA, non-interlaced
```

When opening this image in an image viewer it is clear that it is a QR code.

We can use `zbar` to read the contents of the qr code:

```sh
$ zbarimg JKVhwx.igz 
QR-Code:After what Greek island is the earliest analog computer named after?
scanned 1 barcode symbols from 1 images in 0,14 seconds
```

It seems like the password of the rar file will be the answer to the question:

`After what Greek island is the earliest analog computer named after?`

After a funny googling session, the answer is: `Antikythera`

# Level 2

Here we also get two files:

- FhklxVhwx.pto
- Level2.rar

We will check the filetype with `file` again:

```sh
$ file FhklxVhwx.pto
FhklxVhwx.pto: RIFF (little-endian) data, WAVE audio, Microsoft PCM, 8 bit, mono 11050 Hz
```

When listening to the `wav` file it is clear that it is morse code.

I used [this website](https://morsecode.world/international/decoder/audio-decoder-adaptive.html) to decode the audio. Here I got the following message:

`W H O I N V E N T E D T H E I N F O R M A T I O N B I T ?`

Or simply: `Who invented the information bit?`

Another quick googling session lead me to finding the answer: `Shannon`.

# Level 3

Here we have the files:

- Level3.rar
- Zalnhuvnyhwof.wun

```sh
$ file Zalnhuvnyhwof.wun
Zalnhuvnyhwof.wun: PNG image data, 1024 x 1024, 8-bit/color RGBA, non-interlaced
```

I couldn't find anything interesting in the image itself, so I tried running a tool called `zsteg` on the image and I got the following output:

`Which country first broke Enigma?`

The answer being: `Poland`

# Flag

`DDC{Phr0m_Th3_80770m_70_Th3_70p}`
