# RAG with Azure AI Search¶

| Step         | Tech            | Execution   |
|--------------|-----------------|-------------|
| Embedding    | Azure OpenAI    | 🌐 Remote    |
| Vector Store | Azure AI Search | 🌐 Remote    |
| Gen AI       | Azure OpenAI    | 🌐 Remote    |

## A recipe 🧑‍🍳 🐥 💚¶

This notebook demonstrates how to build a Retrieval-Augmented Generation (RAG) system using:

- Docling for document parsing and chunking
- Azure AI Search for vector indexing and retrieval
- Azure OpenAI for embeddings and chat completion

This sample demonstrates how to:

1. Parse a PDF with Docling.
2. Chunk the parsed text.
3. Use Azure OpenAI for embeddings.
4. Index and search in Azure AI Search.
5. Run a retrieval-augmented generation (RAG) query with Azure OpenAI GPT-4o.

```
# If running in a fresh environment (like Google Colab), uncomment and run this single command:
%pip install "docling~=2.12" azure-search-documents==11.5.2 azure-identity openai rich torch python-dotenv
```

### Part 0: Prerequisites¶

- Azure AI Search resource
- Azure OpenAI resource with a deployed embedding and chat completion model (e.g. text-embedding-3-small and gpt-4o)
- Docling 2.12+ (installs docling\_core automatically)  Docling installed (Python 3.8+ environment)
- A GPU-enabled environment is preferred for faster parsing. Docling 2.12 automatically detects GPU if present.

A GPU-enabled environment is preferred for faster parsing. Docling 2.12 automatically detects GPU if present.

    - If you only have CPU, parsing large PDFs can be slower.

```
import os

from dotenv import load_dotenv

load_dotenv()

def _get_env(key, default=None):
    try:
        from google.colab import userdata

        try:
            return userdata.get(key)
        except userdata.SecretNotFoundError:
            pass
    except ImportError:
        pass
    return os.getenv(key, default)

AZURE_SEARCH_ENDPOINT = _get_env("AZURE_SEARCH_ENDPOINT")
AZURE_SEARCH_KEY = _get_env("AZURE_SEARCH_KEY")  # Ensure this is your Admin Key
AZURE_SEARCH_INDEX_NAME = _get_env("AZURE_SEARCH_INDEX_NAME", "docling-rag-sample")
AZURE_OPENAI_ENDPOINT = _get_env("AZURE_OPENAI_ENDPOINT")
AZURE_OPENAI_API_KEY = _get_env("AZURE_OPENAI_API_KEY")
AZURE_OPENAI_API_VERSION = _get_env("AZURE_OPENAI_API_VERSION", "2024-10-21")
AZURE_OPENAI_CHAT_MODEL = _get_env(
    "AZURE_OPENAI_CHAT_MODEL"
)  # Using a deployed model named "gpt-4o"
AZURE_OPENAI_EMBEDDINGS = _get_env(
    "AZURE_OPENAI_EMBEDDINGS", "text-embedding-3-small"
)  # Using a deployed model named "text-embeddings-3-small"
```

### Part 1: Parse the PDF with Docling¶

We’ll parse the Microsoft GraphRAG Research Paper (~15 pages). Parsing should be relatively quick, even on CPU, but it will be faster on a GPU or MPS device if available.

(If you prefer a different document, simply provide a different URL or local file path.)

```
from rich.console import Console
from rich.panel import Panel

from docling.document_converter import DocumentConverter

console = Console()

# This URL points to the Microsoft GraphRAG Research Paper (arXiv: 2404.16130), ~15 pages
source_url = "https://arxiv.org/pdf/2404.16130"

console.print(
    "[bold yellow]Parsing a ~15-page PDF. The process should be relatively quick, even on CPU...[/bold yellow]"
)
converter = DocumentConverter()
result = converter.convert(source_url)

# Optional: preview the parsed Markdown
md_preview = result.document.export_to_markdown()
console.print(Panel(md_preview[:500] + "...", title="Docling Markdown Preview"))
```

```
Parsing a ~15-page PDF. The process should be relatively quick, even on CPU...
```

```
╭─────────────────────────────────────────── Docling Markdown Preview ────────────────────────────────────────────╮
│ ## From Local to Global: A Graph RAG Approach to Query-Focused Summarization                                    │
│                                                                                                                 │
│ Darren Edge 1†                                                                                                  │
│                                                                                                                 │
│ Ha Trinh 1†                                                                                                     │
│                                                                                                                 │
│ Newman Cheng 2                                                                                                  │
│                                                                                                                 │
│ Joshua Bradley 2                                                                                                │
│                                                                                                                 │
│ Alex Chao 3                                                                                                     │
│                                                                                                                 │
│ Apurva Mody 3                                                                                                   │
│                                                                                                                 │
│ Steven Truitt 2                                                                                                 │
│                                                                                                                 │
│ ## Jonathan Larson 1                                                                                            │
│                                                                                                                 │
│ 1 Microsoft Research 2 Microsoft Strategic Missions and Technologies 3 Microsoft Office of the CTO              │
│                                                                                                                 │
│ { daedge,trinhha,newmancheng,joshbradley,achao,moapurva,steventruitt,jolarso } @microsoft.com                   │
│                                                                                                                 │
│ † These authors contributed equally to this work                                                                │
│                                                                                                                 │
│ ## Abstract                                                                                                     │
│                                                                                                                 │
│ The use of retrieval-augmented gen...                                                                           │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

### Part 2: Hierarchical Chunking¶

We convert the Document into smaller chunks for embedding and indexing. The built-in HierarchicalChunker preserves structure.

```
from docling.chunking import HierarchicalChunker

chunker = HierarchicalChunker()
doc_chunks = list(chunker.chunk(result.document))

all_chunks = []
for idx, c in enumerate(doc_chunks):
    chunk_text = c.text
    all_chunks.append((f"chunk_{idx}", chunk_text))

console.print(f"Total chunks from PDF: {len(all_chunks)}")
```

```
Total chunks from PDF: 106
```

### Part 3: Create Azure AI Search Index and Push Chunk Embeddings¶

We’ll define a vector index in Azure AI Search, then embed each chunk using Azure OpenAI and upload in batches.

```
from azure.core.credentials import AzureKeyCredential
from azure.search.documents.indexes import SearchIndexClient
from azure.search.documents.indexes.models import (
    AzureOpenAIVectorizer,
    AzureOpenAIVectorizerParameters,
    HnswAlgorithmConfiguration,
    SearchableField,
    SearchField,
    SearchFieldDataType,
    SearchIndex,
    SimpleField,
    VectorSearch,
    VectorSearchProfile,
)
from rich.console import Console

console = Console()

VECTOR_DIM = 1536  # Adjust based on your chosen embeddings model

index_client = SearchIndexClient(
    AZURE_SEARCH_ENDPOINT, AzureKeyCredential(AZURE_SEARCH_KEY)
)

def create_search_index(index_name: str):
    # Define fields
    fields = [
        SimpleField(name="chunk_id", type=SearchFieldDataType.String, key=True),
        SearchableField(name="content", type=SearchFieldDataType.String),
        SearchField(
            name="content_vector",
            type=SearchFieldDataType.Collection(SearchFieldDataType.Single),
            searchable=True,
            filterable=False,
            sortable=False,
            facetable=False,
            vector_search_dimensions=VECTOR_DIM,
            vector_search_profile_name="default",
        ),
    ]
    # Vector search config with an AzureOpenAIVectorizer
    vector_search = VectorSearch(
        algorithms=[HnswAlgorithmConfiguration(name="default")],
        profiles=[
            VectorSearchProfile(
                name="default",
                algorithm_configuration_name="default",
                vectorizer_name="default",
            )
        ],
        vectorizers=[
            AzureOpenAIVectorizer(
                vectorizer_name="default",
                parameters=AzureOpenAIVectorizerParameters(
                    resource_url=AZURE_OPENAI_ENDPOINT,
                    deployment_name=AZURE_OPENAI_EMBEDDINGS,
                    model_name="text-embedding-3-small",
                    api_key=AZURE_OPENAI_API_KEY,
                ),
            )
        ],
    )

    # Create or update the index
    new_index = SearchIndex(name=index_name, fields=fields, vector_search=vector_search)
    try:
        index_client.delete_index(index_name)
    except:
        pass

    index_client.create_or_update_index(new_index)
    console.print(f"Index '{index_name}' created.")

create_search_index(AZURE_SEARCH_INDEX_NAME)
```

```
Index 'docling-rag-sample-2' created.
```

#### Generate Embeddings and Upload to Azure AI Search¶

```
from azure.search.documents import SearchClient
from openai import AzureOpenAI

search_client = SearchClient(
    AZURE_SEARCH_ENDPOINT, AZURE_SEARCH_INDEX_NAME, AzureKeyCredential(AZURE_SEARCH_KEY)
)
openai_client = AzureOpenAI(
    api_key=AZURE_OPENAI_API_KEY,
    api_version=AZURE_OPENAI_API_VERSION,
    azure_endpoint=AZURE_OPENAI_ENDPOINT,
)

def embed_text(text: str):
    """
    Helper to generate embeddings with Azure OpenAI.
    """
    response = openai_client.embeddings.create(
        input=text, model=AZURE_OPENAI_EMBEDDINGS
    )
    return response.data[0].embedding

upload_docs = []
for chunk_id, chunk_text in all_chunks:
    embedding_vector = embed_text(chunk_text)
    upload_docs.append(
        {
            "chunk_id": chunk_id,
            "content": chunk_text,
            "content_vector": embedding_vector,
        }
    )

BATCH_SIZE = 50
for i in range(0, len(upload_docs), BATCH_SIZE):
    subset = upload_docs[i : i + BATCH_SIZE]
    resp = search_client.upload_documents(documents=subset)

    all_succeeded = all(r.succeeded for r in resp)
    console.print(
        f"Uploaded batch {i} -> {i+len(subset)}; all_succeeded: {all_succeeded}, "
        f"first_doc_status_code: {resp[0].status_code}"
    )

console.print("All chunks uploaded to Azure Search.")
```

```
Uploaded batch 0 -> 50; all_succeeded: True, first_doc_status_code: 201
```

```
Uploaded batch 50 -> 100; all_succeeded: True, first_doc_status_code: 201
```

```
Uploaded batch 100 -> 106; all_succeeded: True, first_doc_status_code: 201
```

```
All chunks uploaded to Azure Search.
```

### Part 4: Perform RAG over PDF¶

Combine retrieval from Azure AI Search with Azure OpenAI Chat Completions (aka. grounding your LLM)

```
from azure.search.documents.models import VectorizableTextQuery

def generate_chat_response(prompt: str, system_message: str = None):
    """
    Generates a single-turn chat response using Azure OpenAI Chat.
    If you need multi-turn conversation or follow-up queries, you'll have to
    maintain the messages list externally.
    """
    messages = []
    if system_message:
        messages.append({"role": "system", "content": system_message})
    messages.append({"role": "user", "content": prompt})

    completion = openai_client.chat.completions.create(
        model=AZURE_OPENAI_CHAT_MODEL, messages=messages, temperature=0.7
    )
    return completion.choices[0].message.content

user_query = "What are the main advantages of using the Graph RAG approach for query-focused summarization compared to traditional RAG methods?"
user_embed = embed_text(user_query)

vector_query = VectorizableTextQuery(
    text=user_query,  # passing in text for a hybrid search
    k_nearest_neighbors=5,
    fields="content_vector",
)

search_results = search_client.search(
    search_text=user_query, vector_queries=[vector_query], select=["content"], top=10
)

retrieved_chunks = []
for result in search_results:
    snippet = result["content"]
    retrieved_chunks.append(snippet)

context_str = "\n---\n".join(retrieved_chunks)
rag_prompt = f"""
You are an AI assistant helping answering questions about Microsoft GraphRAG.
Use ONLY the text below to answer the user's question.
If the answer isn't in the text, say you don't know.

Context:
{context_str}

Question: {user_query}
Answer:
"""

final_answer = generate_chat_response(rag_prompt)

console.print(Panel(rag_prompt, title="RAG Prompt", style="bold red"))
console.print(Panel(final_answer, title="RAG Response", style="bold green"))
```

```
╭────────────────────────────────────────────────── RAG Prompt ───────────────────────────────────────────────────╮
│                                                                                                                 │
│ You are an AI assistant helping answering questions about Microsoft GraphRAG.                                   │
│ Use ONLY the text below to answer the user's question.                                                          │
│ If the answer isn't in the text, say you don't know.                                                            │
│                                                                                                                 │
│ Context:                                                                                                        │
│ Community summaries vs. source texts. When comparing community summaries to source texts using Graph RAG,       │
│ community summaries generally provided a small but consistent improvement in answer comprehensiveness and       │
│ diversity, except for root-level summaries. Intermediate-level summaries in the Podcast dataset and low-level   │
│ community summaries in the News dataset achieved comprehensiveness win rates of 57% and 64%, respectively.      │
│ Diversity win rates were 57% for Podcast intermediate-level summaries and 60% for News low-level community      │
│ summaries. Table 3 also illustrates the scalability advantages of Graph RAG compared to source text             │
│ summarization: for low-level community summaries ( C3 ), Graph RAG required 26-33% fewer context tokens, while  │
│ for root-level community summaries ( C0 ), it required over 97% fewer tokens. For a modest drop in performance  │
│ compared with other global methods, root-level Graph RAG offers a highly efficient method for the iterative     │
│ question answering that characterizes sensemaking activity, while retaining advantages in comprehensiveness     │
│ (72% win rate) and diversity (62% win rate) over na¨ıve RAG.                                                    │
│ ---                                                                                                             │
│ We have presented a global approach to Graph RAG, combining knowledge graph generation, retrieval-augmented     │
│ generation (RAG), and query-focused summarization (QFS) to support human sensemaking over entire text corpora.  │
│ Initial evaluations show substantial improvements over a na¨ıve RAG baseline for both the comprehensiveness and │
│ diversity of answers, as well as favorable comparisons to a global but graph-free approach using map-reduce     │
│ source text summarization. For situations requiring many global queries over the same dataset, summaries of     │
│ root-level communities in the entity-based graph index provide a data index that is both superior to na¨ıve RAG │
│ and achieves competitive performance to other global methods at a fraction of the token cost.                   │
│ ---                                                                                                             │
│ Trade-offs of building a graph index . We consistently observed Graph RAG achieve the best headto-head results  │
│ against other methods, but in many cases the graph-free approach to global summarization of source texts        │
│ performed competitively. The real-world decision about whether to invest in building a graph index depends on   │
│ multiple factors, including the compute budget, expected number of lifetime queries per dataset, and value      │
│ obtained from other aspects of the graph index (including the generic community summaries and the use of other  │
│ graph-related RAG approaches).                                                                                  │
│ ---                                                                                                             │
│ Future work . The graph index, rich text annotations, and hierarchical community structure supporting the       │
│ current Graph RAG approach offer many possibilities for refinement and adaptation. This includes RAG approaches │
│ that operate in a more local manner, via embedding-based matching of user queries and graph annotations, as     │
│ well as the possibility of hybrid RAG schemes that combine embedding-based matching against community reports   │
│ before employing our map-reduce summarization mechanisms. This 'roll-up' operation could also be extended       │
│ across more levels of the community hierarchy, as well as implemented as a more exploratory 'drill down'        │
│ mechanism that follows the information scent contained in higher-level community summaries.                     │
│ ---                                                                                                             │
│ Advanced RAG systems include pre-retrieval, retrieval, post-retrieval strategies designed to overcome the       │
│ drawbacks of Na¨ıve RAG, while Modular RAG systems include patterns for iterative and dynamic cycles of         │
│ interleaved retrieval and generation (Gao et al., 2023). Our implementation of Graph RAG incorporates multiple  │
│ concepts related to other systems. For example, our community summaries are a kind of self-memory (Selfmem,     │
│ Cheng et al., 2024) for generation-augmented retrieval (GAR, Mao et al., 2020) that facilitates future          │
│ generation cycles, while our parallel generation of community answers from these summaries is a kind of         │
│ iterative (Iter-RetGen, Shao et al., 2023) or federated (FeB4RAG, Wang et al., 2024) retrieval-generation       │
│ strategy. Other systems have also combined these concepts for multi-document summarization (CAiRE-COVID, Su et  │
│ al., 2020) and multi-hop question answering (ITRG, Feng et al., 2023; IR-CoT, Trivedi et al., 2022; DSP,        │
│ Khattab et al., 2022). Our use of a hierarchical index and summarization also bears resemblance to further      │
│ approaches, such as generating a hierarchical index of text chunks by clustering the vectors of text embeddings │
│ (RAPTOR, Sarthi et al., 2024) or generating a 'tree of clarifications' to answer multiple interpretations of    │
│ ambiguous questions (Kim et al., 2023). However, none of these iterative or hierarchical approaches use the     │
│ kind of self-generated graph index that enables Graph RAG.                                                      │
│ ---                                                                                                             │
│ The use of retrieval-augmented generation (RAG) to retrieve relevant information from an external knowledge     │
│ source enables large language models (LLMs) to answer questions over private and/or previously unseen document  │
│ collections. However, RAG fails on global questions directed at an entire text corpus, such as 'What are the    │
│ main themes in the dataset?', since this is inherently a queryfocused summarization (QFS) task, rather than an  │
│ explicit retrieval task. Prior QFS methods, meanwhile, fail to scale to the quantities of text indexed by       │
│ typical RAGsystems. To combine the strengths of these contrasting methods, we propose a Graph RAG approach to   │
│ question answering over private text corpora that scales with both the generality of user questions and the     │
│ quantity of source text to be indexed. Our approach uses an LLM to build a graph-based text index in two        │
│ stages: first to derive an entity knowledge graph from the source documents, then to pregenerate community      │
│ summaries for all groups of closely-related entities. Given a question, each community summary is used to       │
│ generate a partial response, before all partial responses are again summarized in a final response to the user. │
│ For a class of global sensemaking questions over datasets in the 1 million token range, we show that Graph RAG  │
│ leads to substantial improvements over a na¨ıve RAG baseline for both the comprehensiveness and diversity of    │
│ generated answers. An open-source, Python-based implementation of both global and local Graph RAG approaches is │
│ forthcoming at https://aka . ms/graphrag .                                                                      │
│ ---                                                                                                             │
│ Given the multi-stage nature of our Graph RAG mechanism, the multiple conditions we wanted to compare, and the  │
│ lack of gold standard answers to our activity-based sensemaking questions, we decided to adopt a head-to-head   │
│ comparison approach using an LLM evaluator. We selected three target metrics capturing qualities that are       │
│ desirable for sensemaking activities, as well as a control metric (directness) used as a indicator of validity. │
│ Since directness is effectively in opposition to comprehensiveness and diversity, we would not expect any       │
│ method to win across all four metrics.                                                                          │
│ ---                                                                                                             │
│ Figure 1: Graph RAG pipeline using an LLM-derived graph index of source document text. This index spans nodes   │
│ (e.g., entities), edges (e.g., relationships), and covariates (e.g., claims) that have been detected,           │
│ extracted, and summarized by LLM prompts tailored to the domain of the dataset. Community detection (e.g.,      │
│ Leiden, Traag et al., 2019) is used to partition the graph index into groups of elements (nodes, edges,         │
│ covariates) that the LLM can summarize in parallel at both indexing time and query time. The 'global answer' to │
│ a given query is produced using a final round of query-focused summarization over all community summaries       │
│ reporting relevance to that query.                                                                              │
│ ---                                                                                                             │
│ Retrieval-augmented generation (RAG, Lewis et al., 2020) is an established approach to answering user questions │
│ over entire datasets, but it is designed for situations where these answers are contained locally within        │
│ regions of text whose retrieval provides sufficient grounding for the generation task. Instead, a more          │
│ appropriate task framing is query-focused summarization (QFS, Dang, 2006), and in particular, query-focused     │
│ abstractive summarization that generates natural language summaries and not just concatenated excerpts (Baumel  │
│ et al., 2018; Laskar et al., 2020; Yao et al., 2017) . In recent years, however, such distinctions between      │
│ summarization tasks that are abstractive versus extractive, generic versus query-focused, and single-document   │
│ versus multi-document, have become less relevant. While early applications of the transformer architecture      │
│ showed substantial improvements on the state-of-the-art for all such summarization tasks (Goodwin et al., 2020; │
│ Laskar et al., 2022; Liu and Lapata, 2019), these tasks are now trivialized by modern LLMs, including the GPT   │
│ (Achiam et al., 2023; Brown et al., 2020), Llama (Touvron et al., 2023), and Gemini (Anil et al., 2023) series, │
│ all of which can use in-context learning to summarize any content provided in their context window.             │
│ ---                                                                                                             │
│ community descriptions provide complete coverage of the underlying graph index and the input documents it       │
│ represents. Query-focused summarization of an entire corpus is then made possible using a map-reduce approach:  │
│ first using each community summary to answer the query independently and in parallel, then summarizing all      │
│ relevant partial answers into a final global answer.                                                            │
│                                                                                                                 │
│ Question: What are the main advantages of using the Graph RAG approach for query-focused summarization compared │
│ to traditional RAG methods?                                                                                     │
│ Answer:                                                                                                         │
│                                                                                                                 │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

```
╭───────────────────────────────────────────────── RAG Response ──────────────────────────────────────────────────╮
│ The main advantages of using the Graph RAG approach for query-focused summarization compared to traditional RAG │
│ methods include:                                                                                                │
│                                                                                                                 │
│ 1. **Improved Comprehensiveness and Diversity**: Graph RAG shows substantial improvements over a naïve RAG      │
│ baseline in terms of the comprehensiveness and diversity of answers. This is particularly beneficial for global │
│ sensemaking questions over large datasets.                                                                      │
│                                                                                                                 │
│ 2. **Scalability**: Graph RAG provides scalability advantages, achieving efficient summarization with           │
│ significantly fewer context tokens required. For instance, it requires 26-33% fewer tokens for low-level        │
│ community summaries and over 97% fewer tokens for root-level summaries compared to source text summarization.   │
│                                                                                                                 │
│ 3. **Efficiency in Iterative Question Answering**: Root-level Graph RAG offers a highly efficient method for    │
│ iterative question answering, which is crucial for sensemaking activities, with only a modest drop in           │
│ performance compared to other global methods.                                                                   │
│                                                                                                                 │
│ 4. **Global Query Handling**: It supports handling global queries effectively, as it combines knowledge graph   │
│ generation, retrieval-augmented generation, and query-focused summarization, making it suitable for sensemaking │
│ over entire text corpora.                                                                                       │
│                                                                                                                 │
│ 5. **Hierarchical Indexing and Summarization**: The use of a hierarchical index and summarization allows for    │
│ efficient processing and summarizing of community summaries into a final global answer, facilitating a          │
│ comprehensive coverage of the underlying graph index and input documents.                                       │
│                                                                                                                 │
│ 6. **Reduced Token Cost**: For situations requiring many global queries over the same dataset, Graph RAG        │
│ achieves competitive performance to other global methods at a fraction of the token cost.                       │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```