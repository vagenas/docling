# Hybrid chunking¶

## Overview¶

Hybrid chunking applies tokenization-aware refinements on top of document-based hierarchical chunking.

For more details, see here.

## Setup¶

```
%pip install -qU docling transformers
```

```
Note: you may need to restart the kernel to use updated packages.
```

## Conversion¶

```
from docling.document_converter import DocumentConverter

DOC_SOURCE = "../../tests/data/md/wiki.md"

doc = DocumentConverter().convert(source=DOC_SOURCE).document
```

## Chunking¶

### Basic usage¶

For a basic usage scenario, we can just instantiate a HybridChunker, which will use
the default parameters.

```
from docling.chunking import HybridChunker

chunker = HybridChunker()
chunk_iter = chunker.chunk(dl_doc=doc)
```

Note that the text you would typically want to embed is the context-enriched one as
returned by the serialize() method:

```
for i, chunk in enumerate(chunk_iter):
    print(f"=== {i} ===")
    print(f"chunk.text:\n{repr(f'{chunk.text[:300]}…')}")

    enriched_text = chunker.serialize(chunk=chunk)
    print(f"chunker.serialize(chunk):\n{repr(f'{enriched_text[:300]}…')}")

    print()
```

```
=== 0 ===
chunk.text:
'International Business Machines Corporation (using the trademark IBM), nicknamed Big Blue, is an American multinational technology company headquartered in Armonk, New York and present in over 175 countries.\nIt is a publicly traded company and one of the 30 companies in the Dow Jones Industrial Aver…'
chunker.serialize(chunk):
'IBM\nInternational Business Machines Corporation (using the trademark IBM), nicknamed Big Blue, is an American multinational technology company headquartered in Armonk, New York and present in over 175 countries.\nIt is a publicly traded company and one of the 30 companies in the Dow Jones Industrial …'

=== 1 ===
chunk.text:
'IBM originated with several technological innovations developed and commercialized in the late 19th century. Julius E. Pitrap patented the computing scale in 1885;[17] Alexander Dey invented the dial recorder (1888);[18] Herman Hollerith patented the Electric Tabulating Machine (1889);[19] and Willa…'
chunker.serialize(chunk):
'IBM\n1910s–1950s\nIBM originated with several technological innovations developed and commercialized in the late 19th century. Julius E. Pitrap patented the computing scale in 1885;[17] Alexander Dey invented the dial recorder (1888);[18] Herman Hollerith patented the Electric Tabulating Machine (1889…'

=== 2 ===
chunk.text:
'Collectively, the companies manufactured a wide array of machinery for sale and lease, ranging from commercial scales and industrial time recorders, meat and cheese slicers, to tabulators and punched cards. Thomas J. Watson, Sr., fired from the National Cash Register Company by John Henry Patterson,…'
chunker.serialize(chunk):
'IBM\n1910s–1950s\nCollectively, the companies manufactured a wide array of machinery for sale and lease, ranging from commercial scales and industrial time recorders, meat and cheese slicers, to tabulators and punched cards. Thomas J. Watson, Sr., fired from the National Cash Register Company by John …'

=== 3 ===
chunk.text:
'In 1961, IBM developed the SABRE reservation system for American Airlines and introduced the highly successful Selectric typewriter.…'
chunker.serialize(chunk):
'IBM\n1960s–1980s\nIn 1961, IBM developed the SABRE reservation system for American Airlines and introduced the highly successful Selectric typewriter.…'
```

### Advanced usage¶

For more control on the chunking, we can parametrize through the HybridChunker
arguments illustrated below.

Notice how tokenizer and embed\_model further below are single-sourced from
EMBED\_MODEL\_ID.
This is important for making sure the chunker and the embedding model are using the same
tokenizer.

```
from transformers import AutoTokenizer

from docling.chunking import HybridChunker

EMBED_MODEL_ID = "sentence-transformers/all-MiniLM-L6-v2"
MAX_TOKENS = 64  # set to a small number for illustrative purposes

tokenizer = AutoTokenizer.from_pretrained(EMBED_MODEL_ID)

chunker = HybridChunker(
    tokenizer=tokenizer,  # instance or model name, defaults to "sentence-transformers/all-MiniLM-L6-v2"
    max_tokens=MAX_TOKENS,  # optional, by default derived from `tokenizer`
    merge_peers=True,  # optional, defaults to True
)
chunk_iter = chunker.chunk(dl_doc=doc)
chunks = list(chunk_iter)
```

Points to notice looking at the output chunks below:

- Where possible, we fit the limit of 64 tokens for the metadata-enriched serialization form (see chunk 2)
- Where neeeded, we stop before the limit, e.g. see cases of 63 as it would otherwise run into a comma (see chunk 6)
- Where possible, we merge undersized peer chunks (see chunk 0)
- "Tail" chunks trailing right after merges may still be undersized (see chunk 8)

```
for i, chunk in enumerate(chunks):
    print(f"=== {i} ===")
    txt_tokens = len(tokenizer.tokenize(chunk.text, max_length=None))
    print(f"chunk.text ({txt_tokens} tokens):\n{repr(chunk.text)}")

    ser_txt = chunker.serialize(chunk=chunk)
    ser_tokens = len(tokenizer.tokenize(ser_txt, max_length=None))
    print(f"chunker.serialize(chunk) ({ser_tokens} tokens):\n{repr(ser_txt)}")

    print()
```

```
=== 0 ===
chunk.text (55 tokens):
'International Business Machines Corporation (using the trademark IBM), nicknamed Big Blue, is an American multinational technology company headquartered in Armonk, New York and present in over 175 countries.\nIt is a publicly traded company and one of the 30 companies in the Dow Jones Industrial Average.'
chunker.serialize(chunk) (56 tokens):
'IBM\nInternational Business Machines Corporation (using the trademark IBM), nicknamed Big Blue, is an American multinational technology company headquartered in Armonk, New York and present in over 175 countries.\nIt is a publicly traded company and one of the 30 companies in the Dow Jones Industrial Average.'

=== 1 ===
chunk.text (45 tokens):
'IBM is the largest industrial research organization in the world, with 19 research facilities across a dozen countries, having held the record for most annual U.S. patents generated by a business for 29 consecutive years from 1993 to 2021.'
chunker.serialize(chunk) (46 tokens):
'IBM\nIBM is the largest industrial research organization in the world, with 19 research facilities across a dozen countries, having held the record for most annual U.S. patents generated by a business for 29 consecutive years from 1993 to 2021.'

=== 2 ===
chunk.text (63 tokens):
'IBM was founded in 1911 as the Computing-Tabulating-Recording Company (CTR), a holding company of manufacturers of record-keeping and measuring systems. It was renamed "International Business Machines" in 1924 and soon became the leading manufacturer of punch-card tabulating systems. During the 1960s and 1970s, the'
chunker.serialize(chunk) (64 tokens):
'IBM\nIBM was founded in 1911 as the Computing-Tabulating-Recording Company (CTR), a holding company of manufacturers of record-keeping and measuring systems. It was renamed "International Business Machines" in 1924 and soon became the leading manufacturer of punch-card tabulating systems. During the 1960s and 1970s, the'

=== 3 ===
chunk.text (44 tokens):
"IBM mainframe, exemplified by the System/360, was the world's dominant computing platform, with the company producing 80 percent of computers in the U.S. and 70 percent of computers worldwide.[11]"
chunker.serialize(chunk) (45 tokens):
"IBM\nIBM mainframe, exemplified by the System/360, was the world's dominant computing platform, with the company producing 80 percent of computers in the U.S. and 70 percent of computers worldwide.[11]"

=== 4 ===
chunk.text (63 tokens):
'IBM debuted in the microcomputer market in 1981 with the IBM Personal Computer, — its DOS software provided by Microsoft, — which became the basis for the majority of personal computers to the present day.[12] The company later also found success in the portable space with the ThinkPad. Since the 1990s,'
chunker.serialize(chunk) (64 tokens):
'IBM\nIBM debuted in the microcomputer market in 1981 with the IBM Personal Computer, — its DOS software provided by Microsoft, — which became the basis for the majority of personal computers to the present day.[12] The company later also found success in the portable space with the ThinkPad. Since the 1990s,'

=== 5 ===
chunk.text (61 tokens):
'IBM has concentrated on computer services, software, supercomputers, and scientific research; it sold its microcomputer division to Lenovo in 2005. IBM continues to develop mainframes, and its supercomputers have consistently ranked among the most powerful in the world in the 21st century.'
chunker.serialize(chunk) (62 tokens):
'IBM\nIBM has concentrated on computer services, software, supercomputers, and scientific research; it sold its microcomputer division to Lenovo in 2005. IBM continues to develop mainframes, and its supercomputers have consistently ranked among the most powerful in the world in the 21st century.'

=== 6 ===
chunk.text (62 tokens):
"As one of the world's oldest and largest technology companies, IBM has been responsible for several technological innovations, including the automated teller machine (ATM), dynamic random-access memory (DRAM), the floppy disk, the hard disk drive, the magnetic stripe card, the relational database, the SQL programming"
chunker.serialize(chunk) (63 tokens):
"IBM\nAs one of the world's oldest and largest technology companies, IBM has been responsible for several technological innovations, including the automated teller machine (ATM), dynamic random-access memory (DRAM), the floppy disk, the hard disk drive, the magnetic stripe card, the relational database, the SQL programming"

=== 7 ===
chunk.text (63 tokens):
'language, and the UPC barcode. The company has made inroads in advanced computer chips, quantum computing, artificial intelligence, and data infrastructure.[13][14][15] IBM employees and alumni have won various recognitions for their scientific research and inventions, including six Nobel Prizes and six Turing'
chunker.serialize(chunk) (64 tokens):
'IBM\nlanguage, and the UPC barcode. The company has made inroads in advanced computer chips, quantum computing, artificial intelligence, and data infrastructure.[13][14][15] IBM employees and alumni have won various recognitions for their scientific research and inventions, including six Nobel Prizes and six Turing'

=== 8 ===
chunk.text (5 tokens):
'Awards.[16]'
chunker.serialize(chunk) (6 tokens):
'IBM\nAwards.[16]'

=== 9 ===
chunk.text (56 tokens):
'IBM originated with several technological innovations developed and commercialized in the late 19th century. Julius E. Pitrap patented the computing scale in 1885;[17] Alexander Dey invented the dial recorder (1888);[18] Herman Hollerith patented the Electric Tabulating Machine'
chunker.serialize(chunk) (60 tokens):
'IBM\n1910s–1950s\nIBM originated with several technological innovations developed and commercialized in the late 19th century. Julius E. Pitrap patented the computing scale in 1885;[17] Alexander Dey invented the dial recorder (1888);[18] Herman Hollerith patented the Electric Tabulating Machine'

=== 10 ===
chunk.text (60 tokens):
"(1889);[19] and Willard Bundy invented a time clock to record workers' arrival and departure times on a paper tape (1889).[20] On June 16, 1911, their four companies were amalgamated in New York State by Charles Ranlett Flint forming a fifth company, the"
chunker.serialize(chunk) (64 tokens):
"IBM\n1910s–1950s\n(1889);[19] and Willard Bundy invented a time clock to record workers' arrival and departure times on a paper tape (1889).[20] On June 16, 1911, their four companies were amalgamated in New York State by Charles Ranlett Flint forming a fifth company, the"

=== 11 ===
chunk.text (59 tokens):
'Computing-Tabulating-Recording Company (CTR) based in Endicott, New York.[1][21] The five companies had 1,300 employees and offices and plants in Endicott and Binghamton, New York; Dayton, Ohio; Detroit, Michigan; Washington,'
chunker.serialize(chunk) (63 tokens):
'IBM\n1910s–1950s\nComputing-Tabulating-Recording Company (CTR) based in Endicott, New York.[1][21] The five companies had 1,300 employees and offices and plants in Endicott and Binghamton, New York; Dayton, Ohio; Detroit, Michigan; Washington,'

=== 12 ===
chunk.text (13 tokens):
'D.C.; and Toronto, Canada.[22]'
chunker.serialize(chunk) (17 tokens):
'IBM\n1910s–1950s\nD.C.; and Toronto, Canada.[22]'

=== 13 ===
chunk.text (60 tokens):
'Collectively, the companies manufactured a wide array of machinery for sale and lease, ranging from commercial scales and industrial time recorders, meat and cheese slicers, to tabulators and punched cards. Thomas J. Watson, Sr., fired from the National Cash Register Company by John Henry Patterson, called'
chunker.serialize(chunk) (64 tokens):
'IBM\n1910s–1950s\nCollectively, the companies manufactured a wide array of machinery for sale and lease, ranging from commercial scales and industrial time recorders, meat and cheese slicers, to tabulators and punched cards. Thomas J. Watson, Sr., fired from the National Cash Register Company by John Henry Patterson, called'

=== 14 ===
chunk.text (59 tokens):
"on Flint and, in 1914, was offered a position at CTR.[23] Watson joined CTR as general manager and then, 11 months later, was made President when antitrust cases relating to his time at NCR were resolved.[24] Having learned Patterson's pioneering business"
chunker.serialize(chunk) (63 tokens):
"IBM\n1910s–1950s\non Flint and, in 1914, was offered a position at CTR.[23] Watson joined CTR as general manager and then, 11 months later, was made President when antitrust cases relating to his time at NCR were resolved.[24] Having learned Patterson's pioneering business"

=== 15 ===
chunk.text (23 tokens):
"practices, Watson proceeded to put the stamp of NCR onto CTR's companies.[23]:\n105"
chunker.serialize(chunk) (27 tokens):
"IBM\n1910s–1950s\npractices, Watson proceeded to put the stamp of NCR onto CTR's companies.[23]:\n105"

=== 16 ===
chunk.text (59 tokens):
'He implemented sales conventions, "generous sales incentives, a focus on customer service, an insistence on well-groomed, dark-suited salesmen and had an evangelical fervor for instilling company pride and loyalty in every worker".[25][26] His favorite slogan,'
chunker.serialize(chunk) (63 tokens):
'IBM\n1910s–1950s\nHe implemented sales conventions, "generous sales incentives, a focus on customer service, an insistence on well-groomed, dark-suited salesmen and had an evangelical fervor for instilling company pride and loyalty in every worker".[25][26] His favorite slogan,'

=== 17 ===
chunk.text (60 tokens):
'"THINK", became a mantra for each company\'s employees.[25] During Watson\'s first four years, revenues reached $9 million ($158 million today) and the company\'s operations expanded to Europe, South America, Asia and Australia.[25] Watson never liked the'
chunker.serialize(chunk) (64 tokens):
'IBM\n1910s–1950s\n"THINK", became a mantra for each company\'s employees.[25] During Watson\'s first four years, revenues reached $9 million ($158 million today) and the company\'s operations expanded to Europe, South America, Asia and Australia.[25] Watson never liked the'

=== 18 ===
chunk.text (57 tokens):
'clumsy hyphenated name "Computing-Tabulating-Recording Company" and chose to replace it with the more expansive title "International Business Machines" which had previously been used as the name of CTR\'s Canadian Division;[27] the name was changed on February 14,'
chunker.serialize(chunk) (61 tokens):
'IBM\n1910s–1950s\nclumsy hyphenated name "Computing-Tabulating-Recording Company" and chose to replace it with the more expansive title "International Business Machines" which had previously been used as the name of CTR\'s Canadian Division;[27] the name was changed on February 14,'

=== 19 ===
chunk.text (21 tokens):
'1924.[28] By 1933, most of the subsidiaries had been merged into one company, IBM.'
chunker.serialize(chunk) (25 tokens):
'IBM\n1910s–1950s\n1924.[28] By 1933, most of the subsidiaries had been merged into one company, IBM.'

=== 20 ===
chunk.text (22 tokens):
'In 1961, IBM developed the SABRE reservation system for American Airlines and introduced the highly successful Selectric typewriter.'
chunker.serialize(chunk) (26 tokens):
'IBM\n1960s–1980s\nIn 1961, IBM developed the SABRE reservation system for American Airlines and introduced the highly successful Selectric typewriter.'
```