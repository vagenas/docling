# Conversion of custom XML¶

| Step         | Tech                                 | Execution   |
|--------------|--------------------------------------|-------------|
| Embedding    | Hugging Face / Sentence Transformers | 💻 Local     |
| Vector store | Milvus                               | 💻 Local     |
| Gen AI       | Hugging Face Inference API           | 🌐 Remote    |

## Overview¶

This is an example of using Docling for converting structured data (XML) into a unified document
representation format, DoclingDocument, and leverage its riched structured content for RAG applications.

Data used in this example consist of patents from the United States Patent and Trademark Office (USPTO) and medical
articles from PubMed Central® (PMC).

In this notebook, we accomplish the following:

- Simple conversion of supported XML files in a nutshell
- An end-to-end application using public collections of XML files supported by Docling
    - Setup the API access for generative AI
    - Fetch the data from USPTO and PubMed Central® sites, using Docling custom backends
    - Parse, chunk, and index the documents in a vector database
    - Perform RAG using LlamaIndex Docling extension

For more details on document chunking with Docling, refer to the Chunking documentation. For RAG with Docling and LlamaIndex, also check the example RAG with LlamaIndex.

## Simple conversion¶

The XML file format defines and stores data in a format that is both human-readable and machine-readable.
Because of this flexibility, Docling requires custom backend processors to interpret XML definitions and convert them into DoclingDocument objects.

Some public data collections in XML format are already supported by Docling (USTPO patents and PMC articles). In these cases, the document conversion is straightforward and the same as with any other supported format, such as PDF or HTML. The execution example in Simple Conversion is the recommended usage of Docling for a single file:

```
from docling.document_converter import DocumentConverter

# a sample PMC article:
source = "../../tests/data/pubmed/elife-56337.nxml"
converter = DocumentConverter()
result = converter.convert(source)
print(result.status)
```

```
ConversionStatus.SUCCESS
```

Once the document is converted, it can be exported to any format supported by Docling. For instance, to markdown (showing here the first lines only):

```
md_doc = result.document.export_to_markdown()

delim = "\n"
print(delim.join(md_doc.split(delim)[:8]))
```

```
# KRAB-zinc finger protein gene expansion in response to active retrotransposons in the murine lineage

Wolf Gernot; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; de Iaco Alberto; 2: School of Life Sciences, École Polytechnique Fédérale de Lausanne (EPFL): Lausanne: Switzerland; Sun Ming-An; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; Bruno Melania; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; Tinkham Matthew; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; Hoang Don; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; Mitra Apratim; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; Ralls Sherry; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States; Trono Didier; 2: School of Life Sciences, École Polytechnique Fédérale de Lausanne (EPFL): Lausanne: Switzerland; Macfarlan Todd S; 1: The Eunice Kennedy Shriver National Institute of Child Health and Human Development, The National Institutes of Health: Bethesda: United States

## Abstract

The Krüppel-associated box zinc finger protein (KRAB-ZFP) family diversified in mammals. The majority of human KRAB-ZFPs bind transposable elements (TEs), however, since most TEs are inactive in humans it is unclear whether KRAB-ZFPs emerged to suppress TEs. We demonstrate that many recently emerged murine KRAB-ZFPs also bind to TEs, including the active ETn, IAP, and L1 families. Using a CRISPR/Cas9-based engineering approach, we genetically deleted five large clusters of KRAB-ZFPs and demonstrate that target TEs are de-repressed, unleashing TE-encoded enhancers. Homozygous knockout mice lacking one of two KRAB-ZFP gene clusters on chromosome 2 and chromosome 4 were nonetheless viable. In pedigrees of chromosome 4 cluster KRAB-ZFP mutants, we identified numerous novel ETn insertions with a modest increase in mutants. Our data strongly support the current model that recent waves of retrotransposon activity drove the expansion of KRAB-ZFP genes in mice and that many KRAB-ZFPs play a redundant role restricting TE activity.
```

If the XML file is not supported, a ConversionError message will be raised.

```
from io import BytesIO

from docling.datamodel.base_models import DocumentStream
from docling.exceptions import ConversionError

xml_content = (
    b'<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE docling_test SYSTEM '
    b'"test.dtd"><docling>Random content</docling>'
)
stream = DocumentStream(name="docling_test.xml", stream=BytesIO(xml_content))
try:
    result = converter.convert(stream)
except ConversionError as ce:
    print(ce)
```

```
Input document docling_test.xml does not match any allowed format.
```

```
File format not allowed: docling_test.xml
```

You can always refer to the Usage documentation page for a list of supported formats.

## End-to-end application¶

This section describes a step-by-step application for processing XML files from supported public collections and use them for question-answering.

### Setup¶

Requirements can be installed as shown below. The --no-warn-conflicts argument is meant for Colab's pre-populated Python environment, feel free to remove for stricter usage.

```
%pip install -q --progress-bar off --no-warn-conflicts llama-index-core llama-index-readers-docling llama-index-node-parser-docling llama-index-embeddings-huggingface llama-index-llms-huggingface-api llama-index-vector-stores-milvus llama-index-readers-file python-dotenv
```

```
Note: you may need to restart the kernel to use updated packages.
```

This notebook uses HuggingFace's Inference API. For an increased LLM quota, a token can be provided via the environment variable HF\_TOKEN.

If you're running this notebook in Google Colab, make sure you add your API key as a secret.

```
import os
from warnings import filterwarnings

from dotenv import load_dotenv

def _get_env_from_colab_or_os(key):
    try:
        from google.colab import userdata

        try:
            return userdata.get(key)
        except userdata.SecretNotFoundError:
            pass
    except ImportError:
        pass
    return os.getenv(key)

load_dotenv()

filterwarnings(action="ignore", category=UserWarning, module="pydantic")
```

We can now define the main parameters:

```
from pathlib import Path
from tempfile import mkdtemp

from llama_index.embeddings.huggingface import HuggingFaceEmbedding
from llama_index.llms.huggingface_api import HuggingFaceInferenceAPI

EMBED_MODEL_ID = "BAAI/bge-small-en-v1.5"
EMBED_MODEL = HuggingFaceEmbedding(model_name=EMBED_MODEL_ID)
TEMP_DIR = Path(mkdtemp())
MILVUS_URI = str(TEMP_DIR / "docling.db")
GEN_MODEL = HuggingFaceInferenceAPI(
    token=_get_env_from_colab_or_os("HF_TOKEN"),
    model_name="mistralai/Mixtral-8x7B-Instruct-v0.1",
)
embed_dim = len(EMBED_MODEL.get_text_embedding("hi"))
# https://github.com/huggingface/transformers/issues/5486:
os.environ["TOKENIZERS_PARALLELISM"] = "false"
```

### Fetch the data¶

In this notebook we will use XML data from collections supported by Docling:

- Medical articles from the PubMed Central® (PMC). They are available in an FTP server as .tar.gz files. Each file contains the full article data in XML format, among other supplementary files like images or spreadsheets.
- Patents from the United States Patent and Trademark Office. They are available in the Bulk Data Storage System (BDSS) as zip files. Each zip file may contain several patents in XML format.

The raw files will be downloaded form the source and saved in a temporary directory.

#### PMC articles¶

The OA file is a manifest file of all the PMC articles, including the URL path to download the source files. In this notebook we will use as example the article Pathogens spread by high-altitude windborne mosquitoes, which is available in the archive file PMC11703268.tar.gz.

```
import tarfile
from io import BytesIO

import requests

# PMC article PMC11703268
url: str = "https://ftp.ncbi.nlm.nih.gov/pub/pmc/oa_package/e3/6b/PMC11703268.tar.gz"

print(f"Downloading {url}...")
buf = BytesIO(requests.get(url).content)
print("Extracting and storing the XML file containing the article text...")
with tarfile.open(fileobj=buf, mode="r:gz") as tar_file:
    for tarinfo in tar_file:
        if tarinfo.isreg():
            file_path = Path(tarinfo.name)
            if file_path.suffix == ".nxml":
                with open(TEMP_DIR / file_path.name, "wb") as file_obj:
                    file_obj.write(tar_file.extractfile(tarinfo).read())
                print(f"Stored XML file {file_path.name}")
```

```
Downloading https://ftp.ncbi.nlm.nih.gov/pub/pmc/oa_package/e3/6b/PMC11703268.tar.gz...
Extracting and storing the XML file containing the article text...
Stored XML file nihpp-2024.12.26.630351v1.nxml
```

#### USPTO patents¶

Since each USPTO file is a concatenation of several patents, we need to split its content into valid XML pieces. The following code downloads a sample zip file, split its content in sections, and dumps each section as an XML file. For simplicity, this pipeline is shown here in a sequential manner, but it could be parallelized.

```
import zipfile

# Patent grants from December 17-23, 2024
url: str = (
    "https://bulkdata.uspto.gov/data/patent/grant/redbook/fulltext/2024/ipg241217.zip"
)
XML_SPLITTER: str = '<?xml version="1.0"'
doc_num: int = 0

print(f"Downloading {url}...")
buf = BytesIO(requests.get(url).content)
print(f"Parsing zip file, splitting into XML sections, and exporting to files...")
with zipfile.ZipFile(buf) as zf:
    res = zf.testzip()
    if res:
        print("Error validating zip file")
    else:
        with zf.open(zf.namelist()[0]) as xf:
            is_patent = False
            patent_buffer = BytesIO()
            for xf_line in xf:
                decoded_line = xf_line.decode(errors="ignore").rstrip()
                xml_index = decoded_line.find(XML_SPLITTER)
                if xml_index != -1:
                    if (
                        xml_index > 0
                    ):  # cases like </sequence-cwu><?xml version="1.0"...
                        patent_buffer.write(xf_line[:xml_index])
                        patent_buffer.write(b"\r\n")
                        xf_line = xf_line[xml_index:]
                    if patent_buffer.getbuffer().nbytes > 0 and is_patent:
                        doc_num += 1
                        patent_id = f"ipg241217-{doc_num}"
                        with open(TEMP_DIR / f"{patent_id}.xml", "wb") as file_obj:
                            file_obj.write(patent_buffer.getbuffer())
                    is_patent = False
                    patent_buffer = BytesIO()
                elif decoded_line.startswith("<!DOCTYPE"):
                    is_patent = True
                patent_buffer.write(xf_line)
```

```
print(f"Fetched and exported {doc_num} documents.")
```

```
Fetched and exported 4014 documents.
```

### Using the backend converter (optional)¶

- The custom backend converters PubMedDocumentBackend and PatentUsptoDocumentBackend aim at handling the parsing of PMC articles and USPTO patents, respectively.
- As any other backends, you can leverage the function is\_valid() to check if the input document is supported by the this backend.
- Note that some XML sections in the original USPTO zip file may not represent patents, like sequence listings, and therefore they will show as invalid by the backend.

```
from tqdm.notebook import tqdm

from docling.backend.xml.pubmed_backend import PubMedDocumentBackend
from docling.backend.xml.uspto_backend import PatentUsptoDocumentBackend
from docling.datamodel.base_models import InputFormat
from docling.datamodel.document import InputDocument

# check PMC
in_doc = InputDocument(
    path_or_stream=TEMP_DIR / "nihpp-2024.12.26.630351v1.nxml",
    format=InputFormat.XML_PUBMED,
    backend=PubMedDocumentBackend,
)
backend = PubMedDocumentBackend(
    in_doc=in_doc, path_or_stream=TEMP_DIR / "nihpp-2024.12.26.630351v1.nxml"
)
print(f"Document {in_doc.file.name} is a valid PMC article? {backend.is_valid()}")

# check USPTO
in_doc = InputDocument(
    path_or_stream=TEMP_DIR / "ipg241217-1.xml",
    format=InputFormat.XML_USPTO,
    backend=PatentUsptoDocumentBackend,
)
backend = PatentUsptoDocumentBackend(
    in_doc=in_doc, path_or_stream=TEMP_DIR / "ipg241217-1.xml"
)
print(f"Document {in_doc.file.name} is a valid patent? {backend.is_valid()}")

patent_valid = 0
pbar = tqdm(TEMP_DIR.glob("*.xml"), total=doc_num)
for in_path in pbar:
    in_doc = InputDocument(
        path_or_stream=in_path,
        format=InputFormat.XML_USPTO,
        backend=PatentUsptoDocumentBackend,
    )
    backend = PatentUsptoDocumentBackend(in_doc=in_doc, path_or_stream=in_path)
    patent_valid += int(backend.is_valid())

print(f"Found {patent_valid} patents out of {doc_num} XML files.")
```

```
Document nihpp-2024.12.26.630351v1.nxml is a valid PMC article? True
Document ipg241217-1.xml is a valid patent? True
```

```
0%|          | 0/4014 [00:00<?, ?it/s]
```

```
Found 3928 patents out of 4014 XML files.
```

Calling the function convert() will convert the input document into a DoclingDocument

```
doc = backend.convert()

claims_sec = [item for item in doc.texts if item.text == "CLAIMS"][0]
print(f'Patent "{doc.texts[0].text}" has {len(claims_sec.children)} claims')
```

```
Patent "Semiconductor package" has 19 claims
```

✏️ Tip: in general, there is no need to use the backend converters to parse USPTO or PubMed XML files. The generic DocumentConverter object tries to guess the input document format and applies the corresponding backend parser. The conversion shown in Simple Conversion is the recommended usage for the supported XML files.

### Parse, chunk, and index¶

The DoclingDocument format of the converted patents has a rich hierarchical structure, inherited from the original XML document and preserved by the Docling custom backend.
In this notebook, we will leverage:

- The SimpleDirectoryReader pattern to iterate over the exported XML files created in section Fetch the data.
- The LlamaIndex extensions, DoclingReader and DoclingNodeParser, to ingest the patent chunks into a Milvus vectore store.
- The HierarchicalChunker implementation, which applies a document-based hierarchical chunking, to leverage the patent structures like sections and paragraphs within sections.

Refer to other possible implementations and usage patterns in the Chunking documentation and the RAG with LlamaIndex notebook.

##### Set the Docling reader and the directory reader¶

Note that DoclingReader uses Docling's DocumentConverter by default and therefore it will recognize the format of the XML files and leverage the PatentUsptoDocumentBackend automatically.

For demonstration purposes, we limit the scope of the analysis to the first 100 patents.

```
from llama_index.core import SimpleDirectoryReader
from llama_index.readers.docling import DoclingReader

reader = DoclingReader(export_type=DoclingReader.ExportType.JSON)
dir_reader = SimpleDirectoryReader(
    input_dir=TEMP_DIR,
    exclude=["docling.db", "*.nxml"],
    file_extractor={".xml": reader},
    filename_as_id=True,
    num_files_limit=100,
)
```

##### Set the node parser¶

Note that the HierarchicalChunker is the default chunking implementation of the DoclingNodeParser.

```
from llama_index.node_parser.docling import DoclingNodeParser

node_parser = DoclingNodeParser()
```

##### Set a local Milvus database and run the ingestion¶

```
from llama_index.core import StorageContext, VectorStoreIndex
from llama_index.vector_stores.milvus import MilvusVectorStore

vector_store = MilvusVectorStore(
    uri=MILVUS_URI,
    dim=embed_dim,
    overwrite=True,
)

index = VectorStoreIndex.from_documents(
    documents=dir_reader.load_data(show_progress=True),
    transformations=[node_parser],
    storage_context=StorageContext.from_defaults(vector_store=vector_store),
    embed_model=EMBED_MODEL,
    show_progress=True,
)
```

```
2025-01-24 16:49:57,108 [DEBUG][_create_connection]: Created new connection using: 2d58fad6c63448a486c0c0ffe3b7b28c (async_milvus_client.py:600)
Loading files:  51%|█████     | 51/100 [00:00<00:00, 67.88file/s]Input document ipg241217-1050.xml does not match any allowed format.
```

```
Failed to load file /var/folders/2r/b2sdj1512g1_0m7wzzy7sftr0000gn/T/tmp11rjcdj8/ipg241217-1050.xml with error: File format not allowed: /var/folders/2r/b2sdj1512g1_0m7wzzy7sftr0000gn/T/tmp11rjcdj8/ipg241217-1050.xml. Skipping...
```

```
Loading files: 100%|██████████| 100/100 [00:01<00:00, 58.05file/s]
```

```
Parsing nodes:   0%|          | 0/99 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/2048 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/2048 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/2048 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/2048 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/2048 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/2048 [00:00<?, ?it/s]
```

```
Generating embeddings:   0%|          | 0/425 [00:00<?, ?it/s]
```

Finally, add the PMC article to the vector store directly from the reader.

```
index.from_documents(
    documents=reader.load_data(TEMP_DIR / "nihpp-2024.12.26.630351v1.nxml"),
    transformations=[node_parser],
    storage_context=StorageContext.from_defaults(vector_store=vector_store),
    embed_model=EMBED_MODEL,
)
```

```
<llama_index.core.indices.vector_store.base.VectorStoreIndex at 0x373a7f7d0>
```

### Question-answering with RAG¶

The retriever can be used to identify highly relevant documents:

```
retriever = index.as_retriever(similarity_top_k=3)
results = retriever.retrieve("What patents are related to fitness devices?")

for item in results:
    print(item)
```

```
Node ID: 5afd36c0-a739-4a88-a51c-6d0f75358db5
Text: The portable fitness monitoring device 102 may be a device such
as, for example, a mobile phone, a personal digital assistant, a music
file player (e.g. and MP3 player), an intelligent article for wearing
(e.g. a fitness monitoring garment, wrist band, or watch), a dongle
(e.g. a small hardware device that protects software) that includes a
fitn...
Score:  0.772

Node ID: f294b5fd-9089-43cb-8c4e-d1095a634ff1
Text: US Patent Application US 20120071306 entitled “Portable
Multipurpose Whole Body Exercise Device” discloses a portable
multipurpose whole body exercise device which can be used for general
fitness, Pilates-type, core strengthening, therapeutic, and
rehabilitative exercises as well as stretching and physical therapy
and which includes storable acc...
Score:  0.749

Node ID: 8251c7ef-1165-42e1-8c91-c99c8a711bf7
Text: Program products, methods, and systems for providing fitness
monitoring services of the present invention can include any software
application executed by one or more computing devices. A computing
device can be any type of computing device having one or more
processors. For example, a computing device can be a workstation,
mobile device (e.g., ...
Score:  0.744
```

With the query engine, we can run the question-answering with the RAG pattern on the set of indexed documents.

First, we can prompt the LLM directly:

```
from llama_index.core.base.llms.types import ChatMessage, MessageRole
from rich.console import Console
from rich.panel import Panel

console = Console()
query = "Do mosquitoes in high altitude expand viruses over large distances?"

usr_msg = ChatMessage(role=MessageRole.USER, content=query)
response = GEN_MODEL.chat(messages=[usr_msg])

console.print(Panel(query, title="Prompt", border_style="bold red"))
console.print(
    Panel(
        response.message.content.strip(),
        title="Generated Content",
        border_style="bold green",
    )
)
```

```
╭──────────────────────────────────────────────────── Prompt ─────────────────────────────────────────────────────╮
│ Do mosquitoes in high altitude expand viruses over large distances?                                             │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

```
╭─────────────────────────────────────────────── Generated Content ───────────────────────────────────────────────╮
│ Mosquitoes can be found at high altitudes, but their ability to transmit viruses over long distances is not     │
│ primarily dependent on altitude. Mosquitoes are vectors for various diseases, such as malaria, dengue fever,    │
│ and Zika virus, and their transmission range is more closely related to their movement, the presence of a host, │
│ and environmental conditions that support their survival and reproduction.                                      │
│                                                                                                                 │
│ At high altitudes, the environment can be less suitable for mosquitoes due to factors such as colder            │
│ temperatures, lower humidity, and stronger winds, which can limit their population size and distribution.       │
│ However, some species of mosquitoes have adapted to high-altitude environments and can still transmit diseases  │
│ in these areas.                                                                                                 │
│                                                                                                                 │
│ It is possible for mosquitoes to be transported by wind or human activities to higher altitudes, but this is    │
│ not a significant factor in their ability to transmit viruses over long distances. Instead, long-distance       │
│ transmission of viruses is more often associated with human travel and transportation, which can rapidly spread │
│ infected mosquitoes or humans to new areas, leading to the spread of disease.                                   │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

Now, we can compare the response when the model is prompted with the indexed PMC article as supporting context:

```
from llama_index.core.vector_stores import ExactMatchFilter, MetadataFilters

filters = MetadataFilters(
    filters=[
        ExactMatchFilter(key="filename", value="nihpp-2024.12.26.630351v1.nxml"),
    ]
)

query_engine = index.as_query_engine(llm=GEN_MODEL, filter=filters, similarity_top_k=3)
result = query_engine.query(query)

console.print(
    Panel(
        result.response.strip(),
        title="Generated Content with RAG",
        border_style="bold green",
    )
)
```

```
╭────────────────────────────────────────── Generated Content with RAG ───────────────────────────────────────────╮
│ Yes, mosquitoes in high altitude can expand viruses over large distances. A study intercepted 1,017 female      │
│ mosquitoes at altitudes of 120-290 m above ground over Mali and Ghana and screened them for infection with      │
│ arboviruses, plasmodia, and filariae. The study found that 3.5% of the mosquitoes were infected with            │
│ flaviviruses, and 1.1% were infectious. Additionally, the study identified 19 mosquito-borne pathogens,         │
│ including three arboviruses that affect humans (dengue, West Nile, and M’Poko viruses). The study provides      │
│ compelling evidence that mosquito-borne pathogens are often spread by windborne mosquitoes at altitude.         │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```