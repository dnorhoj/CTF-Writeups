# Duck Hans

Proposed difficulty: `Easy`

109 solves

```text
Mens du var på ferie, så blev din favorit and Hans befriet af en anden frihedskæmpene and. Hans blev domesticeret tidligt i sit liv, så du ved at han ikke vil kunne overleve i naturen og er nødt til at få ham tilbage så hurtigt som muligt. Heldigvis lavede den frihedskæmpene and en fejl ved at efterlade dig en note og et billede af stedet hvor Hans blev sat fri. Derudover, så har dit gamle overvågningskamera opfanget noget, men det stoppede med at virke i et minuts tid omkring det tidspunkt hvor den frihedskæmpene and slog til. Du har nu fået fat på netværkets logfiler som er relateret til dit overvågningskamera og du mistænker at det har været udsat for et DDoS(1) angreb.

Kan du finde navnet og IP addressen på den frihedskæmpene and, samt søen hvor Hans blev sat fri?

Aflever flaget i følgende format: DDC{FornavnEfternavn_Sø_IPaddresse}

Fx: DDC{DonnieDuck_Tystrup_000.000.000.00}

Bemærk at det kun er den første del af søens navn som skal afleveres i formatet: “Tystrup” i stedet for “Tystrup Sø”

(1) DDoS: I den her sag er det ikke et Distributed Denial of Service angreb men et Ducking Denial of Service angreb. Forskellen her er at angrebet måske ikke er fuldstændigt distribueret (“Distributed”).
```

Attachments:

```text
Archive:  duck-hans.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2023-11-27 11:10   material/
  1278222  2023-11-24 15:19   material/Hans_Is_Free.jpg
    20572  2023-11-27 10:57   material/Network Log.txt
    25120  2023-11-24 15:38   material/WARNING!1!1!!!1.pdf
---------                     -------
  1323914                     4 files
```

# Solution

## Finding the Name

The file `WARNING!1!1!!!1.pdf` has embedded exif data, including the creator's email address.

The email address is `quarl.quackington@mail.dk`.

From this we can extrapolate that the name is **Qual Quackington**.

## Finding the Lake

The file `Hans_Is_Free.jpg` has embedded exif data, including some GPS coordinates.

`GPS Position: 55 deg 40' 37.31" N, 12 deg 28' 33.15" E`

Finding these coordinates on a map shows us that this picture was taken at "**Damhus** Sø".

## Finding the IP Address

The archive contains a file called `Network Log.txt`. Here is a short snippet of the contents:

```text
Timestamp           | Source IP       | Destination IP | Protocol | Port | Action
-------------------------------------------------------------------------------------
2023-11-24 04:54:40 | 192.0.2.145     | 10.20.30.40    | TCP      | 80   | Allowed
2023-11-24 04:54:42 | 198.51.100.148  | 10.20.30.40    | TCP      | 80   | Allowed
2023-11-24 04:54:44 | 203.0.113.157   | 10.20.30.40    | TCP      | 80   | Allowed
2023-11-24 04:54:46 | 192.0.2.147     | 10.20.30.40    | TCP      | 443  | Allowed
2023-11-24 04:54:48 | 198.51.100.150  | 10.20.30.40    | TCP      | 80   | Allowed
```

I used the following command to display and sort all the present source IP addresses:

`$ cat Network\ Log.txt | cut -d ' ' -f4 | sort`

A quick look shows us that the IP address `93.184.216.34` has a lot of occurences in comparison to the others.

# Flag

`DDC{QuarlQuackington_Damhus_93.184.216.34}`
