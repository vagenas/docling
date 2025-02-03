# LlamaIndex

Docling is available as an official LlamaIndex extension.

To get started, check out the step-by-step guide in LlamaIndex.

## Components

### Docling Reader

Reads document files and uses Docling to populate LlamaIndex Document objects — either serializing Docling's data model (losslessly, e.g. as JSON) or exporting to a simplified format (lossily, e.g. as Markdown).

- 💻 Docling Reader GitHub
- 📖 Docling Reader docs
- 📦 Docling Reader PyPI

### Docling Node Parser

Reads LlamaIndex Document objects populated in Docling's format by Docling Reader and, using its knowledge of the Docling format, parses them to LlamaIndex Node objects for downstream usage in LlamaIndex applications, e.g. as chunks for embedding.

- 💻 Docling Node Parser GitHub
- 📖 Docling Node Parser docs
- 📦 Docling Node Parser PyPI