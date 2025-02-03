# Document converter

This is an automatic generated API reference of the main components of Docling.

## document\_converter

Classes:

- DocumentConverter
          –
- ConversionResult
          –
- ConversionStatus
          –
- FormatOption
          –
- InputFormat
          –
          
A document format supported by document backend parsers.
- PdfFormatOption
          –
- ImageFormatOption
          –
- StandardPdfPipeline
          –
- WordFormatOption
          –
- PowerpointFormatOption
          –
- MarkdownFormatOption
          –
- AsciiDocFormatOption
          –
- HTMLFormatOption
          –
- SimplePipeline
          –
          
SimpleModelPipeline.

### DocumentConverter

```
DocumentConverter(
    allowed_formats: Optional[List[InputFormat]] = None,
    format_options: Optional[
        Dict[InputFormat, FormatOption]
    ] = None,
)
```

Methods:

- convert
            –
- convert\_all
            –
- initialize\_pipeline
            –
            
Initialize the conversion pipeline for the selected format.

Attributes:

- allowed\_formats
          –
- format\_to\_options
          –
- initialized\_pipelines
              (Dict[Type[BasePipeline], BasePipeline])
          –

#### allowed\_formats

instance-attribute

```
allowed_formats = (
    allowed_formats
    if allowed_formats is not None
    else [e for e in InputFormat]
)
```

#### format\_to\_options

instance-attribute

```
format_to_options = {format: _get_default_option(format=format) if (custom_option := get(format)) is None else _e36Z7ahFuugqfor format in allowed_formats}
```

#### initialized\_pipelines

instance-attribute

```
initialized_pipelines: Dict[
    Type[BasePipeline], BasePipeline
] = {}
```

#### convert

```
convert(
    source: Union[Path, str, DocumentStream],
    headers: Optional[Dict[str, str]] = None,
    raises_on_error: bool = True,
    max_num_pages: int = maxsize,
    max_file_size: int = maxsize,
    page_range: PageRange = DEFAULT_PAGE_RANGE,
) -> ConversionResult
```

#### convert\_all

```
convert_all(
    source: Iterable[Union[Path, str, DocumentStream]],
    headers: Optional[Dict[str, str]] = None,
    raises_on_error: bool = True,
    max_num_pages: int = maxsize,
    max_file_size: int = maxsize,
    page_range: PageRange = DEFAULT_PAGE_RANGE,
) -> Iterator[ConversionResult]
```

#### initialize\_pipeline

```
initialize_pipeline(format: InputFormat)
```

Initialize the conversion pipeline for the selected format.

### ConversionResult

Bases: BaseModel

Attributes:

- assembled
              (AssembledUnit)
          –
- document
              (DoclingDocument)
          –
- errors
              (List[ErrorItem])
          –
- input
              (InputDocument)
          –
- legacy\_document
          –
- pages
              (List[Page])
          –
- status
              (ConversionStatus)
          –
- timings
              (Dict[str, ProfilingItem])
          –

#### assembled

class-attribute
instance-attribute

```
assembled: AssembledUnit = AssembledUnit()
```

#### document

class-attribute
instance-attribute

```
document: DoclingDocument = _EMPTY_DOCLING_DOC
```

#### errors

class-attribute
instance-attribute

```
errors: List[ErrorItem] = []
```

#### input

instance-attribute

```
input: InputDocument
```

#### legacy\_document

property

```
legacy_document
```

#### pages

class-attribute
instance-attribute

```
pages: List[Page] = []
```

#### status

class-attribute
instance-attribute

```
status: ConversionStatus = PENDING
```

#### timings

class-attribute
instance-attribute

```
timings: Dict[str, ProfilingItem] = {}
```

### ConversionStatus

Bases: str, Enum

Attributes:

- FAILURE
          –
- PARTIAL\_SUCCESS
          –
- PENDING
          –
- SKIPPED
          –
- STARTED
          –
- SUCCESS
          –

#### FAILURE

class-attribute
instance-attribute

```
FAILURE = 'failure'
```

#### PARTIAL\_SUCCESS

class-attribute
instance-attribute

```
PARTIAL_SUCCESS = 'partial_success'
```

#### PENDING

class-attribute
instance-attribute

```
PENDING = 'pending'
```

#### SKIPPED

class-attribute
instance-attribute

```
SKIPPED = 'skipped'
```

#### STARTED

class-attribute
instance-attribute

```
STARTED = 'started'
```

#### SUCCESS

class-attribute
instance-attribute

```
SUCCESS = 'success'
```

### FormatOption

Bases: BaseModel

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type[BasePipeline])
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

instance-attribute

```
backend: Type[AbstractDocumentBackend]
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

instance-attribute

```
pipeline_cls: Type[BasePipeline]
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### InputFormat

Bases: str, Enum

A document format supported by document backend parsers.

Attributes:

- ASCIIDOC
          –
- DOCX
          –
- HTML
          –
- IMAGE
          –
- JSON\_DOCLING
          –
- MD
          –
- PDF
          –
- PPTX
          –
- XLSX
          –
- XML\_PUBMED
          –
- XML\_USPTO
          –

#### ASCIIDOC

class-attribute
instance-attribute

```
ASCIIDOC = 'asciidoc'
```

#### DOCX

class-attribute
instance-attribute

```
DOCX = 'docx'
```

#### HTML

class-attribute
instance-attribute

```
HTML = 'html'
```

#### IMAGE

class-attribute
instance-attribute

```
IMAGE = 'image'
```

#### JSON\_DOCLING

class-attribute
instance-attribute

```
JSON_DOCLING = 'json_docling'
```

#### MD

class-attribute
instance-attribute

```
MD = 'md'
```

#### PDF

class-attribute
instance-attribute

```
PDF = 'pdf'
```

#### PPTX

class-attribute
instance-attribute

```
PPTX = 'pptx'
```

#### XLSX

class-attribute
instance-attribute

```
XLSX = 'xlsx'
```

#### XML\_PUBMED

class-attribute
instance-attribute

```
XML_PUBMED = 'xml_pubmed'
```

#### XML\_USPTO

class-attribute
instance-attribute

```
XML_USPTO = 'xml_uspto'
```

### PdfFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = (
    DoclingParseV2DocumentBackend
)
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = StandardPdfPipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### ImageFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = (
    DoclingParseV2DocumentBackend
)
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = StandardPdfPipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### StandardPdfPipeline

```
StandardPdfPipeline(pipeline_options: PdfPipelineOptions)
```

Bases: PaginatedPipeline

Methods:

- download\_models\_hf
            –
- execute
            –
- get\_default\_options
            –
- get\_ocr\_model
            –
- initialize\_page
            –
- is\_backend\_supported
            –

Attributes:

- artifacts\_path
          –
- build\_pipe
          –
- enrichment\_pipe
          –
- glm\_model
          –
- keep\_backend
          –
- keep\_images
          –
- pipeline\_options
              (PdfPipelineOptions)
          –

#### artifacts\_path

instance-attribute

```
artifacts_path = download_models_hf()
```

#### build\_pipe

instance-attribute

```
build_pipe = [
    PagePreprocessingModel(
        options=PagePreprocessingOptions(
            images_scale=images_scale
        )
    ),
    ocr_model,
    LayoutModel(
        artifacts_path=artifacts_path / _layout_model_path,
        accelerator_options=accelerator_options,
    ),
    TableStructureModel(
        enabled=do_table_structure,
        artifacts_path=artifacts_path / _table_model_path,
        options=table_structure_options,
        accelerator_options=accelerator_options,
    ),
    PageAssembleModel(options=PageAssembleOptions()),
]
```

#### enrichment\_pipe

instance-attribute

```
enrichment_pipe = [
    CodeFormulaModel(
        enabled=do_code_enrichment or do_formula_enrichment,
        artifacts_path=artifacts_path,
        options=CodeFormulaModelOptions(
            do_code_enrichment=do_code_enrichment,
            do_formula_enrichment=do_formula_enrichment,
        ),
        accelerator_options=accelerator_options,
    ),
    DocumentPictureClassifier(
        enabled=do_picture_classification,
        artifacts_path=artifacts_path,
        options=DocumentPictureClassifierOptions(),
        accelerator_options=accelerator_options,
    ),
]
```

#### glm\_model

instance-attribute

```
glm_model = GlmModel(options=GlmOptions())
```

#### keep\_backend

instance-attribute

```
keep_backend = True
```

#### keep\_images

instance-attribute

```
keep_images = (
    generate_page_images
    or generate_picture_images
    or generate_table_images
)
```

#### pipeline\_options

instance-attribute

```
pipeline_options: PdfPipelineOptions
```

#### download\_models\_hf

staticmethod

```
download_models_hf(
    local_dir: Optional[Path] = None, force: bool = False
) -> Path
```

#### execute

```
execute(
    in_doc: InputDocument, raises_on_error: bool
) -> ConversionResult
```

#### get\_default\_options

classmethod

```
get_default_options() -> PdfPipelineOptions
```

#### get\_ocr\_model

```
get_ocr_model() -> Optional[BaseOcrModel]
```

#### initialize\_page

```
initialize_page(
    conv_res: ConversionResult, page: Page
) -> Page
```

#### is\_backend\_supported

classmethod

```
is_backend_supported(backend: AbstractDocumentBackend)
```

### WordFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = (
    MsWordDocumentBackend
)
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = SimplePipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### PowerpointFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = (
    MsPowerpointDocumentBackend
)
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = SimplePipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### MarkdownFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = (
    MarkdownDocumentBackend
)
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = SimplePipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### AsciiDocFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = AsciiDocBackend
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = SimplePipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### HTMLFormatOption

Bases: FormatOption

Methods:

- set\_optional\_field\_default
            –

Attributes:

- backend
              (Type[AbstractDocumentBackend])
          –
- model\_config
          –
- pipeline\_cls
              (Type)
          –
- pipeline\_options
              (Optional[PipelineOptions])
          –

#### backend

class-attribute
instance-attribute

```
backend: Type[AbstractDocumentBackend] = HTMLDocumentBackend
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(arbitrary_types_allowed=True)
```

#### pipeline\_cls

class-attribute
instance-attribute

```
pipeline_cls: Type = SimplePipeline
```

#### pipeline\_options

class-attribute
instance-attribute

```
pipeline_options: Optional[PipelineOptions] = None
```

#### set\_optional\_field\_default

```
set_optional_field_default() -> FormatOption
```

### SimplePipeline

```
SimplePipeline(pipeline_options: PipelineOptions)
```

Bases: BasePipeline

SimpleModelPipeline.

This class is used at the moment for formats / backends
which produce straight DoclingDocument output.

Methods:

- execute
            –
- get\_default\_options
            –
- is\_backend\_supported
            –

Attributes:

- build\_pipe
              (List[Callable])
          –
- enrichment\_pipe
              (List[GenericEnrichmentModel[Any]])
          –
- keep\_images
          –
- pipeline\_options
          –

#### build\_pipe

instance-attribute

```
build_pipe: List[Callable] = []
```

#### enrichment\_pipe

instance-attribute

```
enrichment_pipe: List[GenericEnrichmentModel[Any]] = []
```

#### keep\_images

instance-attribute

```
keep_images = False
```

#### pipeline\_options

instance-attribute

```
pipeline_options = pipeline_options
```

#### execute

```
execute(
    in_doc: InputDocument, raises_on_error: bool
) -> ConversionResult
```

#### get\_default\_options

classmethod

```
get_default_options() -> PipelineOptions
```

#### is\_backend\_supported

classmethod

```
is_backend_supported(backend: AbstractDocumentBackend)
```