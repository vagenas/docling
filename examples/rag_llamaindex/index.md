# RAG with LlamaIndex¶

| Step         | Tech                                 | Execution   |
|--------------|--------------------------------------|-------------|
| Embedding    | Hugging Face / Sentence Transformers | 💻 Local     |
| Vector store | Milvus                               | 💻 Local     |
| Gen AI       | Hugging Face Inference API           | 🌐 Remote    |

## Overview¶

This example leverages the official LlamaIndex Docling extension.

Presented extensions DoclingReader and DoclingNodeParser enable you to:

- use various document types in your LLM applications with ease and speed, and
- leverage Docling's rich format for advanced, document-native grounding.

## Setup¶

- 👉 For best conversion speed, use GPU acceleration whenever available; e.g. if running on Colab, use GPU-enabled runtime.
- Notebook uses HuggingFace's Inference API; for increased LLM quota, token can be provided via env var HF\_TOKEN.
- Requirements can be installed as shown below (--no-warn-conflicts meant for Colab's pre-populated Python env; feel free to remove for stricter usage):

```
%pip install -q --progress-bar off --no-warn-conflicts llama-index-core llama-index-readers-docling llama-index-node-parser-docling llama-index-embeddings-huggingface llama-index-llms-huggingface-api llama-index-vector-stores-milvus llama-index-readers-file python-dotenv
```

```
Note: you may need to restart the kernel to use updated packages.
```

```
import os
from pathlib import Path
from tempfile import mkdtemp
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
filterwarnings(action="ignore", category=FutureWarning, module="easyocr")
# https://github.com/huggingface/transformers/issues/5486:
os.environ["TOKENIZERS_PARALLELISM"] = "false"
```

We can now define the main parameters:

```
from llama_index.embeddings.huggingface import HuggingFaceEmbedding
from llama_index.llms.huggingface_api import HuggingFaceInferenceAPI

EMBED_MODEL = HuggingFaceEmbedding(model_name="BAAI/bge-small-en-v1.5")
MILVUS_URI = str(Path(mkdtemp()) / "docling.db")
GEN_MODEL = HuggingFaceInferenceAPI(
    token=_get_env_from_colab_or_os("HF_TOKEN"),
    model_name="mistralai/Mixtral-8x7B-Instruct-v0.1",
)
SOURCE = "https://arxiv.org/pdf/2408.09869"  # Docling Technical Report
QUERY = "Which are the main AI models in Docling?"

embed_dim = len(EMBED_MODEL.get_text_embedding("hi"))
```

## Using Markdown export¶

To create a simple RAG pipeline, we can:

- define a DoclingReader, which by default exports to Markdown, and
- use a standard node parser for these Markdown-based docs, e.g. a MarkdownNodeParser

```
from llama_index.core import StorageContext, VectorStoreIndex
from llama_index.core.node_parser import MarkdownNodeParser
from llama_index.readers.docling import DoclingReader
from llama_index.vector_stores.milvus import MilvusVectorStore

reader = DoclingReader()
node_parser = MarkdownNodeParser()

vector_store = MilvusVectorStore(
    uri=str(Path(mkdtemp()) / "docling.db"),  # or set as needed
    dim=embed_dim,
    overwrite=True,
)
index = VectorStoreIndex.from_documents(
    documents=reader.load_data(SOURCE),
    transformations=[node_parser],
    storage_context=StorageContext.from_defaults(vector_store=vector_store),
    embed_model=EMBED_MODEL,
)
result = index.as_query_engine(llm=GEN_MODEL).query(QUERY)
print(f"Q: {QUERY}\nA: {result.response.strip()}\n\nSources:")
display([(n.text, n.metadata) for n in result.source_nodes])
```

```
Q: Which are the main AI models in Docling?
A: The main AI models in Docling are a layout analysis model, which is an accurate object-detector for page elements, and TableFormer, a state-of-the-art table structure recognition model.

Sources:
```

```
[('3.2 AI models\n\nAs part of Docling, we initially release two highly capable AI models to the open-source community, which have been developed and published recently by our team. The first model is a layout analysis model, an accurate object-detector for page elements [13]. The second model is TableFormer [12, 9], a state-of-the-art table structure recognition model. We provide the pre-trained weights (hosted on huggingface) and a separate package for the inference code as docling-ibm-models . Both models are also powering the open-access deepsearch-experience, our cloud-native service for knowledge exploration tasks.',
  {'Header_2': '3.2 AI models'}),
 ("5 Applications\n\nThanks to the high-quality, richly structured document conversion achieved by Docling, its output qualifies for numerous downstream applications. For example, Docling can provide a base for detailed enterprise document search, passage retrieval or classification use-cases, or support knowledge extraction pipelines, allowing specific treatment of different structures in the document, such as tables, figures, section structure or references. For popular generative AI application patterns, such as retrieval-augmented generation (RAG), we provide quackling , an open-source package which capitalizes on Docling's feature-rich document output to enable document-native optimized vector embedding and chunking. It plugs in seamlessly with LLM frameworks such as LlamaIndex [8]. Since Docling is fast, stable and cheap to run, it also makes for an excellent choice to build document-derived datasets. With its powerful table structure recognition, it provides significant benefit to automated knowledge-base construction [11, 10]. Docling is also integrated within the open IBM data prep kit [6], which implements scalable data transforms to build large-scale multi-modal training datasets.",
  {'Header_2': '5 Applications'})]
```

## Using Docling format¶

To leverage Docling's rich native format, we:

- create a DoclingReader with JSON export type, and
- employ a DoclingNodeParser in order to appropriately parse that Docling format.

Notice how the sources now also contain document-level grounding (e.g. page number or bounding box information):

```
from llama_index.node_parser.docling import DoclingNodeParser

reader = DoclingReader(export_type=DoclingReader.ExportType.JSON)
node_parser = DoclingNodeParser()

vector_store = MilvusVectorStore(
    uri=str(Path(mkdtemp()) / "docling.db"),  # or set as needed
    dim=embed_dim,
    overwrite=True,
)
index = VectorStoreIndex.from_documents(
    documents=reader.load_data(SOURCE),
    transformations=[node_parser],
    storage_context=StorageContext.from_defaults(vector_store=vector_store),
    embed_model=EMBED_MODEL,
)
result = index.as_query_engine(llm=GEN_MODEL).query(QUERY)
print(f"Q: {QUERY}\nA: {result.response.strip()}\n\nSources:")
display([(n.text, n.metadata) for n in result.source_nodes])
```

```
Q: Which are the main AI models in Docling?
A: The main AI models in Docling are a layout analysis model and TableFormer. The layout analysis model is an accurate object-detector for page elements, and TableFormer is a state-of-the-art table structure recognition model.

Sources:
```

```
[('As part of Docling, we initially release two highly capable AI models to the open-source community, which have been developed and published recently by our team. The first model is a layout analysis model, an accurate object-detector for page elements [13]. The second model is TableFormer [12, 9], a state-of-the-art table structure recognition model. We provide the pre-trained weights (hosted on huggingface) and a separate package for the inference code as docling-ibm-models . Both models are also powering the open-access deepsearch-experience, our cloud-native service for knowledge exploration tasks.',
  {'schema_name': 'docling_core.transforms.chunker.DocMeta',
   'version': '1.0.0',
   'doc_items': [{'self_ref': '#/texts/34',
     'parent': {'$ref': '#/body'},
     'children': [],
     'label': 'text',
     'prov': [{'page_no': 3,
       'bbox': {'l': 107.07593536376953,
        't': 406.1695251464844,
        'r': 504.1148681640625,
        'b': 330.2677307128906,
        'coord_origin': 'BOTTOMLEFT'},
       'charspan': [0, 608]}]}],
   'headings': ['3.2 AI models'],
   'origin': {'mimetype': 'application/pdf',
    'binary_hash': 14981478401387673002,
    'filename': '2408.09869v3.pdf'}}),
 ('With Docling , we open-source a very capable and efficient document conversion tool which builds on the powerful, specialized AI models and datasets for layout analysis and table structure recognition we developed and presented in the recent past [12, 13, 9]. Docling is designed as a simple, self-contained python library with permissive license, running entirely locally on commodity hardware. Its code architecture allows for easy extensibility and addition of new features and models.',
  {'schema_name': 'docling_core.transforms.chunker.DocMeta',
   'version': '1.0.0',
   'doc_items': [{'self_ref': '#/texts/9',
     'parent': {'$ref': '#/body'},
     'children': [],
     'label': 'text',
     'prov': [{'page_no': 1,
       'bbox': {'l': 107.0031967163086,
        't': 136.7283935546875,
        'r': 504.04998779296875,
        'b': 83.30133056640625,
        'coord_origin': 'BOTTOMLEFT'},
       'charspan': [0, 488]}]}],
   'headings': ['1 Introduction'],
   'origin': {'mimetype': 'application/pdf',
    'binary_hash': 14981478401387673002,
    'filename': '2408.09869v3.pdf'}})]
```

## With Simple Directory Reader¶

To demonstrate this usage pattern, we first set up a test document directory.

```
from pathlib import Path
from tempfile import mkdtemp

import requests

tmp_dir_path = Path(mkdtemp())
r = requests.get(SOURCE)
with open(tmp_dir_path / f"{Path(SOURCE).name}.pdf", "wb") as out_file:
    out_file.write(r.content)
```

Using the reader and node\_parser definitions from any of the above variants, usage with SimpleDirectoryReader then looks as follows:

```
from llama_index.core import SimpleDirectoryReader

dir_reader = SimpleDirectoryReader(
    input_dir=tmp_dir_path,
    file_extractor={".pdf": reader},
)

vector_store = MilvusVectorStore(
    uri=str(Path(mkdtemp()) / "docling.db"),  # or set as needed
    dim=embed_dim,
    overwrite=True,
)
index = VectorStoreIndex.from_documents(
    documents=dir_reader.load_data(SOURCE),
    transformations=[node_parser],
    storage_context=StorageContext.from_defaults(vector_store=vector_store),
    embed_model=EMBED_MODEL,
)
result = index.as_query_engine(llm=GEN_MODEL).query(QUERY)
print(f"Q: {QUERY}\nA: {result.response.strip()}\n\nSources:")
display([(n.text, n.metadata) for n in result.source_nodes])
```

```
Loading files: 100%|██████████| 1/1 [00:11<00:00, 11.27s/file]
```

```
Q: Which are the main AI models in Docling?
A: 1. A layout analysis model, an accurate object-detector for page elements. 2. TableFormer, a state-of-the-art table structure recognition model.

Sources:
```

```
[('As part of Docling, we initially release two highly capable AI models to the open-source community, which have been developed and published recently by our team. The first model is a layout analysis model, an accurate object-detector for page elements [13]. The second model is TableFormer [12, 9], a state-of-the-art table structure recognition model. We provide the pre-trained weights (hosted on huggingface) and a separate package for the inference code as docling-ibm-models . Both models are also powering the open-access deepsearch-experience, our cloud-native service for knowledge exploration tasks.',
  {'file_path': '/var/folders/76/4wwfs06x6835kcwj4186c0nc0000gn/T/tmp2ooyusg5/2408.09869.pdf',
   'file_name': '2408.09869.pdf',
   'file_type': 'application/pdf',
   'file_size': 5566574,
   'creation_date': '2024-10-28',
   'last_modified_date': '2024-10-28',
   'schema_name': 'docling_core.transforms.chunker.DocMeta',
   'version': '1.0.0',
   'doc_items': [{'self_ref': '#/texts/34',
     'parent': {'$ref': '#/body'},
     'children': [],
     'label': 'text',
     'prov': [{'page_no': 3,
       'bbox': {'l': 107.07593536376953,
        't': 406.1695251464844,
        'r': 504.1148681640625,
        'b': 330.2677307128906,
        'coord_origin': 'BOTTOMLEFT'},
       'charspan': [0, 608]}]}],
   'headings': ['3.2 AI models'],
   'origin': {'mimetype': 'application/pdf',
    'binary_hash': 14981478401387673002,
    'filename': '2408.09869.pdf'}}),
 ('With Docling , we open-source a very capable and efficient document conversion tool which builds on the powerful, specialized AI models and datasets for layout analysis and table structure recognition we developed and presented in the recent past [12, 13, 9]. Docling is designed as a simple, self-contained python library with permissive license, running entirely locally on commodity hardware. Its code architecture allows for easy extensibility and addition of new features and models.',
  {'file_path': '/var/folders/76/4wwfs06x6835kcwj4186c0nc0000gn/T/tmp2ooyusg5/2408.09869.pdf',
   'file_name': '2408.09869.pdf',
   'file_type': 'application/pdf',
   'file_size': 5566574,
   'creation_date': '2024-10-28',
   'last_modified_date': '2024-10-28',
   'schema_name': 'docling_core.transforms.chunker.DocMeta',
   'version': '1.0.0',
   'doc_items': [{'self_ref': '#/texts/9',
     'parent': {'$ref': '#/body'},
     'children': [],
     'label': 'text',
     'prov': [{'page_no': 1,
       'bbox': {'l': 107.0031967163086,
        't': 136.7283935546875,
        'r': 504.04998779296875,
        'b': 83.30133056640625,
        'coord_origin': 'BOTTOMLEFT'},
       'charspan': [0, 488]}]}],
   'headings': ['1 Introduction'],
   'origin': {'mimetype': 'application/pdf',
    'binary_hash': 14981478401387673002,
    'filename': '2408.09869.pdf'}})]
```