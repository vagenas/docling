# Supported formats

Docling can parse various documents formats into a unified representation (Docling
Document), which it can export to different formats too — check out
Architecture for more details.

Below you can find a listing of all supported input and output formats.

## Supported input formats

| Format               | Description                                                  |
|----------------------|--------------------------------------------------------------|
| PDF                  |                                                              |
| DOCX, XLSX, PPTX     | Default formats in MS Office 2007+, based on Office Open XML |
| Markdown             |                                                              |
| AsciiDoc             |                                                              |
| HTML, XHTML          |                                                              |
| PNG, JPEG, TIFF, BMP | Image formats                                                |

Schema-specific support:

| Format       | Description                                     |
|--------------|-------------------------------------------------|
| USPTO XML    | XML format followed by USPTO patents            |
| PMC XML      | XML format followed by PubMed Central® articles |
| Docling JSON | JSON-serialized Docling Document                |

## Supported output formats

| Format   | Description                                        |
|----------|----------------------------------------------------|
| HTML     | Both image embedding and referencing are supported |
| Markdown |                                                    |
| JSON     | Lossless serialization of Docling Document         |
| Text     | Plain text, i.e. without Markdown markers          |
| Doctags  |                                                    |