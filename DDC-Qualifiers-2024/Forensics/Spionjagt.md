# Spionjagt

Proposed difficulty: `Very Easy`

134 solves

```text
Velkommen til "Spionjagt"! Din mission er at afsløre en spion, der har infiltreret et dansk CTF-hold og stjålet følsomme data. Se om du kan identificere de stjålne oplysninger ved at kigge i netværks trafikken.
```

Attachments:

`spionjagt.pcap: pcap capture file, microsecond ts (little-endian) - version 2.4 (Ethernet, capture length 262144)`

# Unintended solution

I initially solved the challenge by just running strings and grepping for the flag, which works...

```bash
$ strings spionjagt.pcap | grep -i ddc
DDcb
vDDdc
nkning. En af mine personlige favoritter var opgaven med flagget i formatet DDC{fa4fb1ed-3124-4702-bb28-a29f57a7cc4a}, hvor l
```

# Intended Solution

I opened wireshark and noticed an FTP connection. By filtering by `ftp-data`, we notice one packet, which is the data being transferred after the command `STOR secretreview.txt` had been executed.

The packet includes a lot of text including the flag.

# Flag

`DDC{fa4fb1ed-3124-4702-bb28-a29f57a7cc4a}`
