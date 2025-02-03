# FAQ

This is a collection of FAQ collected from the user questions on https://github.com/DS4SD/docling/discussions.

### Is Python 3.13 supported?

Python 3.13 is supported from Docling 2.18.0.

### Install conflicts with numpy (python 3.13)

When using docling-ibm-models&gt;=2.0.7 and deepsearch-glm&gt;=0.26.2 these issues should not show up anymore.
Docling supports numpy versions &gt;=1.24.4,&lt;3.0.0 which should match all usages.

For older versions

This has been observed installing docling and langchain via poetry.

```
...
Thus, docling (>=2.7.0,<3.0.0) requires numpy (>=1.26.4,<2.0.0).
So, because ... depends on both numpy (>=2.0.2,<3.0.0) and docling (^2.7.0), version solving failed.
```

Numpy is only adding Python 3.13 support starting in some 2.x.y version. In order to prepare for 3.13, Docling depends on a 2.x.y for 3.13, otherwise depending an 1.x.y version. If you are allowing 3.13 in your pyproject.toml, Poetry will try to find some way to reconcile Docling's numpy version for 3.13 (some 2.x.y) with LangChain's version for that (some 1.x.y) — leading to the error above.

Check if Python 3.13 is among the Python versions allowed by your pyproject.toml and if so, remove it and try again.
E.g., if you have python = "^3.10", use python = "&gt;=3.10,&lt;3.13" instead.

If you want to retain compatibility with python 3.9-3.13, you can also use a selector in pyproject.toml similar to the following

```
numpy = [
    { version = "^2.1.0", markers = 'python_version >= "3.13"' },
    { version = "^1.24.4", markers = 'python_version < "3.13"' },
]
```

Source: Issue #283

### Are text styles (bold, underline, etc) supported?

Currently text styles are not supported in the DoclingDocument format.
If you are interest in contributing this feature, please open a discussion topic to brainstorm on the design.

Note: this is not a simple topic

### How do I run completely offline?

Docling is not using any remote service, hence it can run in completely isolated air-gapped environments.

The only requirement is pointing the Docling runtime to the location where the model artifacts have been stored.

For example

```
pipeline_options = PdfPipelineOptions(artifacts_path="your location")
converter = DocumentConverter(
    format_options={
        InputFormat.PDF: PdfFormatOption(pipeline_options=pipeline_options)
    }
)
```

Source: Issue #326

### Which model weights are needed to run Docling?

Model weights are needed for the AI models used in the PDF pipeline. Other document types (docx, pptx, etc) do not have any such requirement.

For processing PDF documents, Docling requires the model weights from https://huggingface.co/ds4sd/docling-models.

When OCR is enabled, some engines also require model artifacts. For example EasyOCR, for which Docling has special pipeline options to control the runtime behavior.

### SSL error downloading model weights

```
URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1000)>
```

Similar SSL download errors have been observed by some users. This happens when model weights are fetched from Hugging Face.
The error could happen when the python environment doesn't have an up-to-date list of trusted certificates.

Possible solutions were

- Update to the latest version of certifi, i.e. pip install --upgrade certifi
- Use pip-system-certs to use the latest trusted certificates on your system.
- Set environment variables SSL\_CERT\_FILE and REQUESTS\_CA\_BUNDLE to the value of python -m certifi:
    CERT\_PATH=$(python -m certifi)
export SSL_CERT_FILE=${CERT\_PATH}
export REQUESTS\_CA\_BUNDLE=${CERT\_PATH}

### Which OCR languages are supported?

Docling supports multiple OCR engine, each one has its own list of supported languages.
Here is a collection of links to the original OCR engine's documentation listing the OCR languages.

- EasyOCR
- Tesseract
- RapidOCR
- Mac OCR

Setting the OCR language in Docling is done via the OCR pipeline options:

```
from docling.datamodel.pipeline_options import PdfPipelineOptions

pipeline_options = PdfPipelineOptions()
pipeline_options.ocr_options.lang = ["fr", "de", "es", "en"]  # example of languages for EasyOCR
```

??? Some images are missing from MS Word and Powerpoint"

```
### Some images are missing from MS Word and Powerpoint

The image processing library used by Docling is able to handle embedded WMF images only on Windows platform.
If you are on other operaring systems, these images will be ignored.
```