# Hi Grandma

Proposed difficulty: `Easy`

35 solves - 100 points

```text
Din mormor har snakket med dig om et dokument som din bror (Benjamin) angiveligt har sendt hende for at spørge hende om penge til hans studie. Hun synes ikke at kontonummeret er som det plejer og har derfor tøvet med at overføre og ville høre dig, hendes kløgtige barnebarn, om du kunne hjælpe hende. Du ved at der er en funktion “document” i biblioteket docx i python som måske ville kunne bruges her. Se om du kan finde noget underligt ved det her dokument og hjælpe din mormor!

Ting du tænker kunne være vigtige: Hvornår blev det sidst ændret? (UTC) Hvem ændrede det sidst? Hvor mange gange er det blevet ændret?

Flag format: Klokkeslæt, navn og antal gange dokumentet er blevet ændret

Example flag: DDC{1330_Lars_Larsen_23}
```

Attachments:

```text
Archive:  Misc_Hi_Grandma.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2024-04-03 15:19   Misc_Hi_Grandma/
      210  2024-04-03 15:19   __MACOSX/._Misc_Hi_Grandma
    11584  2024-04-01 17:44   Misc_Hi_Grandma/Hi_Grandma.docx
      500  2024-04-01 17:44   __MACOSX/Misc_Hi_Grandma/._Hi_Grandma.docx
---------                     -------
    12294                     4 files
```

# Solution

To solve this I used the python library "docx" as hinted to in the description.

```py
import docx

doc = docx.Document("./Misc_Hi_Grandma/Hi_Grandma.docx")

doc.core_properties.modified
#: datetime.datetime(2024, 4, 1, 15, 44, tzinfo=datetime.timezone.utc)

doc.core_properties.last_modified_by
#: 'Thomas Jefferson'

doc.core_properties.revision
#: 3
```

# Flag

`DDC{1544_Thomas_Jefferson_3}`
