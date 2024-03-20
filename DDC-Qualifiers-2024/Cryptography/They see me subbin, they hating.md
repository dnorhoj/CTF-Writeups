# They see me subbin, they hating

Proposed difficulty: `Easy`

158 solves

```text
Jeg krypterede en masse tilfældige engelske ord med en substitutions-chiffer.

Kan du arbejde med nøglen og dekryptere flaget?

Challengen handler om at kigge på fordeling af bogstaver, som kan være lidt irriterende at implementere selv. Men husk der eksistere en del tooling på internettet i forvejen, så hvis i sidder fast prøve at google lidt omkring tooling til substitution ciphers!
```

Attached files:

`ciphertext.txt: ASCII text, with very long lines (3046)`

# Solution

This is a simple substitution cipher. I used [dCode's monoalphabetic substitution cipher tools](https://www.dcode.fr/monoalphabetic-substitution).

This could automatically find the key, and give us the flag!

# Flag

`DDC{WELCOME_TO_DDC_TWENTY_TWENTY_FOUR_LETS_DO_SOME_CRYPTO}`
