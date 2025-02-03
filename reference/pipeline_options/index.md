# Pipeline options

Pipeline options allow to customize the execution of the models during the conversion pipeline.
This includes options for the OCR engines, the table model as well as enrichment options which
can be enabled with do\_xyz = True.

This is an automatic generated API reference of the all the pipeline options available in Docling.

## pipeline\_options

Classes:

- AcceleratorDevice
          –
          
Devices to run model inference
- AcceleratorOptions
          –
- EasyOcrOptions
          –
          
Options for the EasyOCR engine.
- OcrEngine
          –
          
Enum of valid OCR engines.
- OcrMacOptions
          –
          
Options for the Mac OCR engine.
- OcrOptions
          –
          
OCR options.
- PdfBackend
          –
          
Enum of valid PDF backends.
- PdfPipelineOptions
          –
          
Options for the PDF pipeline.
- PipelineOptions
          –
          
Base pipeline options.
- RapidOcrOptions
          –
          
Options for the RapidOCR engine.
- TableFormerMode
          –
          
Modes for the TableFormer model.
- TableStructureOptions
          –
          
Options for the table structure.
- TesseractCliOcrOptions
          –
          
Options for the TesseractCli engine.
- TesseractOcrOptions
          –
          
Options for the Tesseract engine.

### AcceleratorDevice

Bases: str, Enum

Devices to run model inference

Attributes:

- AUTO
          –
- CPU
          –
- CUDA
          –
- MPS
          –

#### AUTO

class-attribute
instance-attribute

```
AUTO = 'auto'
```

#### CPU

class-attribute
instance-attribute

```
CPU = 'cpu'
```

#### CUDA

class-attribute
instance-attribute

```
CUDA = 'cuda'
```

#### MPS

class-attribute
instance-attribute

```
MPS = 'mps'
```

### AcceleratorOptions

Bases: BaseSettings

Methods:

- check\_alternative\_envvars
            –
            
Set num\_threads from the "alternative" envvar OMP\_NUM\_THREADS.

Attributes:

- device
              (AcceleratorDevice)
          –
- model\_config
          –
- num\_threads
              (int)
          –

#### device

class-attribute
instance-attribute

```
device: AcceleratorDevice = AUTO
```

#### model\_config

class-attribute
instance-attribute

```
model_config = SettingsConfigDict(
    env_prefix="DOCLING_",
    env_nested_delimiter="_",
    populate_by_name=True,
)
```

#### num\_threads

class-attribute
instance-attribute

```
num_threads: int = 4
```

#### check\_alternative\_envvars

classmethod

```
check_alternative_envvars(data: Any) -> Any
```

Set num\_threads from the "alternative" envvar OMP\_NUM\_THREADS.
The alternative envvar is used only if it is valid and the regular envvar is not set.

Notice: The standard pydantic settings mechanism with parameter "aliases" does not provide
the same functionality. In case the alias envvar is set and the user tries to override the
parameter in settings initialization, Pydantic treats the parameter provided in init()
as an extra input instead of simply overwriting the evvar value for that parameter.

### EasyOcrOptions

Bases: OcrOptions

Options for the EasyOCR engine.

Attributes:

- bitmap\_area\_threshold
              (float)
          –
- confidence\_threshold
              (float)
          –
- download\_enabled
              (bool)
          –
- force\_full\_page\_ocr
              (bool)
          –
- kind
              (Literal['easyocr'])
          –
- lang
              (List[str])
          –
- model\_config
          –
- model\_storage\_directory
              (Optional[str])
          –
- recog\_network
              (Optional[str])
          –
- use\_gpu
              (Optional[bool])
          –

#### bitmap\_area\_threshold

class-attribute
instance-attribute

```
bitmap_area_threshold: float = 0.05
```

#### confidence\_threshold

class-attribute
instance-attribute

```
confidence_threshold: float = 0.5
```

#### download\_enabled

class-attribute
instance-attribute

```
download_enabled: bool = True
```

#### force\_full\_page\_ocr

class-attribute
instance-attribute

```
force_full_page_ocr: bool = False
```

#### kind

class-attribute
instance-attribute

```
kind: Literal['easyocr'] = 'easyocr'
```

#### lang

class-attribute
instance-attribute

```
lang: List[str] = ['fr', 'de', 'es', 'en']
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(
    extra="forbid", protected_namespaces=()
)
```

#### model\_storage\_directory

class-attribute
instance-attribute

```
model_storage_directory: Optional[str] = None
```

#### recog\_network

class-attribute
instance-attribute

```
recog_network: Optional[str] = 'standard'
```

#### use\_gpu

class-attribute
instance-attribute

```
use_gpu: Optional[bool] = None
```

### OcrEngine

Bases: str, Enum

Enum of valid OCR engines.

Attributes:

- EASYOCR
          –
- OCRMAC
          –
- RAPIDOCR
          –
- TESSERACT
          –
- TESSERACT\_CLI
          –

#### EASYOCR

class-attribute
instance-attribute

```
EASYOCR = 'easyocr'
```

#### OCRMAC

class-attribute
instance-attribute

```
OCRMAC = 'ocrmac'
```

#### RAPIDOCR

class-attribute
instance-attribute

```
RAPIDOCR = 'rapidocr'
```

#### TESSERACT

class-attribute
instance-attribute

```
TESSERACT = 'tesseract'
```

#### TESSERACT\_CLI

class-attribute
instance-attribute

```
TESSERACT_CLI = 'tesseract_cli'
```

### OcrMacOptions

Bases: OcrOptions

Options for the Mac OCR engine.

Attributes:

- bitmap\_area\_threshold
              (float)
          –
- force\_full\_page\_ocr
              (bool)
          –
- framework
              (str)
          –
- kind
              (Literal['ocrmac'])
          –
- lang
              (List[str])
          –
- model\_config
          –
- recognition
              (str)
          –

#### bitmap\_area\_threshold

class-attribute
instance-attribute

```
bitmap_area_threshold: float = 0.05
```

#### force\_full\_page\_ocr

class-attribute
instance-attribute

```
force_full_page_ocr: bool = False
```

#### framework

class-attribute
instance-attribute

```
framework: str = 'vision'
```

#### kind

class-attribute
instance-attribute

```
kind: Literal['ocrmac'] = 'ocrmac'
```

#### lang

class-attribute
instance-attribute

```
lang: List[str] = ['fr-FR', 'de-DE', 'es-ES', 'en-US']
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(extra='forbid')
```

#### recognition

class-attribute
instance-attribute

```
recognition: str = 'accurate'
```

### OcrOptions

Bases: BaseModel

OCR options.

Attributes:

- bitmap\_area\_threshold
              (float)
          –
- force\_full\_page\_ocr
              (bool)
          –
- kind
              (str)
          –
- lang
              (List[str])
          –

#### bitmap\_area\_threshold

class-attribute
instance-attribute

```
bitmap_area_threshold: float = 0.05
```

#### force\_full\_page\_ocr

class-attribute
instance-attribute

```
force_full_page_ocr: bool = False
```

#### kind

instance-attribute

```
kind: str
```

#### lang

instance-attribute

```
lang: List[str]
```

### PdfBackend

Bases: str, Enum

Enum of valid PDF backends.

Attributes:

- DLPARSE\_V1
          –
- DLPARSE\_V2
          –
- PYPDFIUM2
          –

#### DLPARSE\_V1

class-attribute
instance-attribute

```
DLPARSE_V1 = 'dlparse_v1'
```

#### DLPARSE\_V2

class-attribute
instance-attribute

```
DLPARSE_V2 = 'dlparse_v2'
```

#### PYPDFIUM2

class-attribute
instance-attribute

```
PYPDFIUM2 = 'pypdfium2'
```

### PdfPipelineOptions

Bases: PipelineOptions

Options for the PDF pipeline.

Attributes:

- accelerator\_options
              (AcceleratorOptions)
          –
- artifacts\_path
              (Optional[Union[Path, str]])
          –
- create\_legacy\_output
              (bool)
          –
- do\_code\_enrichment
              (bool)
          –
- do\_formula\_enrichment
              (bool)
          –
- do\_ocr
              (bool)
          –
- do\_picture\_classification
              (bool)
          –
- do\_table\_structure
              (bool)
          –
- document\_timeout
              (Optional[float])
          –
- generate\_page\_images
              (bool)
          –
- generate\_picture\_images
              (bool)
          –
- generate\_table\_images
              (bool)
          –
- images\_scale
              (float)
          –
- ocr\_options
              (Union[EasyOcrOptions, TesseractCliOcrOptions, TesseractOcrOptions, OcrMacOptions, RapidOcrOptions])
          –
- table\_structure\_options
              (TableStructureOptions)
          –

#### accelerator\_options

class-attribute
instance-attribute

```
accelerator_options: AcceleratorOptions = (
    AcceleratorOptions()
)
```

#### artifacts\_path

class-attribute
instance-attribute

```
artifacts_path: Optional[Union[Path, str]] = None
```

#### create\_legacy\_output

class-attribute
instance-attribute

```
create_legacy_output: bool = True
```

#### do\_code\_enrichment

class-attribute
instance-attribute

```
do_code_enrichment: bool = False
```

#### do\_formula\_enrichment

class-attribute
instance-attribute

```
do_formula_enrichment: bool = False
```

#### do\_ocr

class-attribute
instance-attribute

```
do_ocr: bool = True
```

#### do\_picture\_classification

class-attribute
instance-attribute

```
do_picture_classification: bool = False
```

#### do\_table\_structure

class-attribute
instance-attribute

```
do_table_structure: bool = True
```

#### document\_timeout

class-attribute
instance-attribute

```
document_timeout: Optional[float] = None
```

#### generate\_page\_images

class-attribute
instance-attribute

```
generate_page_images: bool = False
```

#### generate\_picture\_images

class-attribute
instance-attribute

```
generate_picture_images: bool = False
```

#### generate\_table\_images

class-attribute
instance-attribute

```
generate_table_images: bool = Field(
    default=False,
    deprecated="Field `generate_table_images` is deprecated. To obtain table images, set `PdfPipelineOptions.generate_page_images = True` before conversion and then use the `TableItem.get_image` function.",
)
```

#### images\_scale

class-attribute
instance-attribute

```
images_scale: float = 1.0
```

#### ocr\_options

class-attribute
instance-attribute

```
ocr_options: Union[
    EasyOcrOptions,
    TesseractCliOcrOptions,
    TesseractOcrOptions,
    OcrMacOptions,
    RapidOcrOptions,
] = Field(EasyOcrOptions(), discriminator="kind")
```

#### table\_structure\_options

class-attribute
instance-attribute

```
table_structure_options: TableStructureOptions = (
    TableStructureOptions()
)
```

### PipelineOptions

Bases: BaseModel

Base pipeline options.

Attributes:

- accelerator\_options
              (AcceleratorOptions)
          –
- create\_legacy\_output
              (bool)
          –
- document\_timeout
              (Optional[float])
          –

#### accelerator\_options

class-attribute
instance-attribute

```
accelerator_options: AcceleratorOptions = (
    AcceleratorOptions()
)
```

#### create\_legacy\_output

class-attribute
instance-attribute

```
create_legacy_output: bool = True
```

#### document\_timeout

class-attribute
instance-attribute

```
document_timeout: Optional[float] = None
```

### RapidOcrOptions

Bases: OcrOptions

Options for the RapidOCR engine.

Attributes:

- bitmap\_area\_threshold
              (float)
          –
- cls\_model\_path
              (Optional[str])
          –
- det\_model\_path
              (Optional[str])
          –
- force\_full\_page\_ocr
              (bool)
          –
- kind
              (Literal['rapidocr'])
          –
- lang
              (List[str])
          –
- model\_config
          –
- print\_verbose
              (bool)
          –
- rec\_keys\_path
              (Optional[str])
          –
- rec\_model\_path
              (Optional[str])
          –
- text\_score
              (float)
          –
- use\_cls
              (Optional[bool])
          –
- use\_det
              (Optional[bool])
          –
- use\_rec
              (Optional[bool])
          –

#### bitmap\_area\_threshold

class-attribute
instance-attribute

```
bitmap_area_threshold: float = 0.05
```

#### cls\_model\_path

class-attribute
instance-attribute

```
cls_model_path: Optional[str] = None
```

#### det\_model\_path

class-attribute
instance-attribute

```
det_model_path: Optional[str] = None
```

#### force\_full\_page\_ocr

class-attribute
instance-attribute

```
force_full_page_ocr: bool = False
```

#### kind

class-attribute
instance-attribute

```
kind: Literal['rapidocr'] = 'rapidocr'
```

#### lang

class-attribute
instance-attribute

```
lang: List[str] = ['english', 'chinese']
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(extra='forbid')
```

#### print\_verbose

class-attribute
instance-attribute

```
print_verbose: bool = False
```

#### rec\_keys\_path

class-attribute
instance-attribute

```
rec_keys_path: Optional[str] = None
```

#### rec\_model\_path

class-attribute
instance-attribute

```
rec_model_path: Optional[str] = None
```

#### text\_score

class-attribute
instance-attribute

```
text_score: float = 0.5
```

#### use\_cls

class-attribute
instance-attribute

```
use_cls: Optional[bool] = None
```

#### use\_det

class-attribute
instance-attribute

```
use_det: Optional[bool] = None
```

#### use\_rec

class-attribute
instance-attribute

```
use_rec: Optional[bool] = None
```

### TableFormerMode

Bases: str, Enum

Modes for the TableFormer model.

Attributes:

- ACCURATE
          –
- FAST
          –

#### ACCURATE

class-attribute
instance-attribute

```
ACCURATE = 'accurate'
```

#### FAST

class-attribute
instance-attribute

```
FAST = 'fast'
```

### TableStructureOptions

Bases: BaseModel

Options for the table structure.

Attributes:

- do\_cell\_matching
              (bool)
          –
- mode
              (TableFormerMode)
          –

#### do\_cell\_matching

class-attribute
instance-attribute

```
do_cell_matching: bool = True
```

#### mode

class-attribute
instance-attribute

```
mode: TableFormerMode = FAST
```

### TesseractCliOcrOptions

Bases: OcrOptions

Options for the TesseractCli engine.

Attributes:

- bitmap\_area\_threshold
              (float)
          –
- force\_full\_page\_ocr
              (bool)
          –
- kind
              (Literal['tesseract'])
          –
- lang
              (List[str])
          –
- model\_config
          –
- path
              (Optional[str])
          –
- tesseract\_cmd
              (str)
          –

#### bitmap\_area\_threshold

class-attribute
instance-attribute

```
bitmap_area_threshold: float = 0.05
```

#### force\_full\_page\_ocr

class-attribute
instance-attribute

```
force_full_page_ocr: bool = False
```

#### kind

class-attribute
instance-attribute

```
kind: Literal['tesseract'] = 'tesseract'
```

#### lang

class-attribute
instance-attribute

```
lang: List[str] = ['fra', 'deu', 'spa', 'eng']
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(extra='forbid')
```

#### path

class-attribute
instance-attribute

```
path: Optional[str] = None
```

#### tesseract\_cmd

class-attribute
instance-attribute

```
tesseract_cmd: str = 'tesseract'
```

### TesseractOcrOptions

Bases: OcrOptions

Options for the Tesseract engine.

Attributes:

- bitmap\_area\_threshold
              (float)
          –
- force\_full\_page\_ocr
              (bool)
          –
- kind
              (Literal['tesserocr'])
          –
- lang
              (List[str])
          –
- model\_config
          –
- path
              (Optional[str])
          –

#### bitmap\_area\_threshold

class-attribute
instance-attribute

```
bitmap_area_threshold: float = 0.05
```

#### force\_full\_page\_ocr

class-attribute
instance-attribute

```
force_full_page_ocr: bool = False
```

#### kind

class-attribute
instance-attribute

```
kind: Literal['tesserocr'] = 'tesserocr'
```

#### lang

class-attribute
instance-attribute

```
lang: List[str] = ['fra', 'deu', 'spa', 'eng']
```

#### model\_config

class-attribute
instance-attribute

```
model_config = ConfigDict(extra='forbid')
```

#### path

class-attribute
instance-attribute

```
path: Optional[str] = None
```