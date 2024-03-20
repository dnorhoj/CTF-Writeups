# CTF Platformen

Proposed difficulty: `Very Easy`

157 solves

```text
Jeg har fundet nogle underlige logs fra en CTF platform der har været med til BlackHat Europe. Det ser dog ud til, at den skriver nogle af flagene ud i loggen. Kan du finde et til denne opgave?
```

Attachments:

`hknd.log: ASCII text`

# Solution

```bash
$ grep -i DDC hknd.log 
Feb 15 19:59:10 sec02 hknd[2261187]: #033[90m7:59PM#033[0m #033[32mINF#033[0m Flag is created for team CyberPossessed [assignlab function]  #033[36mchal-tag=#033[0mscan-1 #033[36mchal-val=#033[0mDDC{CTRL+F-can-be-used-for-flags}
```

# Flag

`DDC{CTRL+F-can-be-used-for-flags}`
