# Docling Document

This is an automatic generated API reference of the DoclingDocument type.

## doc

Package for models defined by the Document type.

Classes:

- DoclingDocument
          –
          
DoclingDocument.
- DocumentOrigin
          –
          
FileSource.
- DocItem
          –
          
DocItem.
- DocItemLabel
          –
          
DocItemLabel.
- ProvenanceItem
          –
          
ProvenanceItem.
- GroupItem
          –
          
GroupItem.
- GroupLabel
          –
          
GroupLabel.
- NodeItem
          –
          
NodeItem.
- PageItem
          –
          
PageItem.
- FloatingItem
          –
          
FloatingItem.
- TextItem
          –
          
TextItem.
- TableItem
          –
          
TableItem.
- TableCell
          –
          
TableCell.
- TableData
          –
          
BaseTableData.
- TableCellLabel
          –
          
TableCellLabel.
- KeyValueItem
          –
          
KeyValueItem.
- SectionHeaderItem
          –
          
SectionItem.
- PictureItem
          –
          
PictureItem.
- ImageRef
          –
          
ImageRef.
- PictureClassificationClass
          –
          
PictureClassificationData.
- PictureClassificationData
          –
          
PictureClassificationData.
- RefItem
          –
          
RefItem.
- BoundingBox
          –
          
BoundingBox.
- CoordOrigin
          –
          
CoordOrigin.
- ImageRefMode
          –
          
ImageRefMode.
- Size
          –
          
Size.

### DoclingDocument

Bases: BaseModel

DoclingDocument.

Methods:

- add\_code
            –
            
add\_code.
- add\_group
            –
            
add\_group.
- add\_heading
            –
            
add\_heading.
- add\_list\_item
            –
            
add\_list\_item.
- add\_page
            –
            
add\_page.
- add\_picture
            –
            
add\_picture.
- add\_table
            –
            
add\_table.
- add\_text
            –
            
add\_text.
- add\_title
            –
            
add\_title.
- check\_version\_is\_compatible
            –
            
Check if this document version is compatible with current version.
- export\_to\_dict
            –
            
Export to dict.
- export\_to\_document\_tokens
            –
            
Exports the document content to a DocumentToken format.
- export\_to\_element\_tree
            –
            
Export\_to\_element\_tree.
- export\_to\_html
            –
            
Serialize to HTML.
- export\_to\_markdown
            –
            
Serialize to Markdown.
- export\_to\_text
            –
            
export\_to\_text.
- iterate\_items
            –
            
iterate\_elements.
- load\_from\_json
            –
            
load\_from\_json.
- num\_pages
            –
            
num\_pages.
- print\_element\_tree
            –
            
Print\_element\_tree.
- save\_as\_document\_tokens
            –
            
Save the document content to a DocumentToken format.
- save\_as\_html
            –
            
Save to HTML.
- save\_as\_json
            –
            
Save as json.
- save\_as\_markdown
            –
            
Save to markdown.
- save\_as\_yaml
            –
            
Save as yaml.
- validate\_document
            –
            
validate\_document.
- validate\_tree
            –
            
validate\_tree.

Attributes:

- body
              (GroupItem)
          –
- furniture
              (GroupItem)
          –
- groups
              (List[GroupItem])
          –
- key\_value\_items
              (List[KeyValueItem])
          –
- name
              (str)
          –
- origin
              (Optional[DocumentOrigin])
          –
- pages
              (Dict[int, PageItem])
          –
- pictures
              (List[PictureItem])
          –
- schema\_name
              (Literal['DoclingDocument'])
          –
- tables
              (List[TableItem])
          –
- texts
              (List[Union[SectionHeaderItem, ListItem, TextItem, CodeItem]])
          –
- version
              (Annotated[str, StringConstraints(pattern=VERSION\_PATTERN, strict=True)])
          –

#### body

```
body: GroupItem = GroupItem(
    name="_root_", self_ref="#/body"
)
```

#### furniture

```
furniture: GroupItem = GroupItem(
    name="_root_", self_ref="#/furniture"
)
```

#### groups

```
groups: List[GroupItem] = []
```

#### key\_value\_items

```
key_value_items: List[KeyValueItem] = []
```

#### name

```
name: str
```

#### origin

```
origin: Optional[DocumentOrigin] = None
```

#### pages

```
pages: Dict[int, PageItem] = {}
```

#### pictures

```
pictures: List[PictureItem] = []
```

#### schema\_name

```
schema_name: Literal['DoclingDocument'] = 'DoclingDocument'
```

#### tables

```
tables: List[TableItem] = []
```

#### texts

```
texts: List[
    Union[SectionHeaderItem, ListItem, TextItem, CodeItem]
] = []
```

#### version

```
version: Annotated[
    str,
    StringConstraints(pattern=VERSION_PATTERN, strict=True),
] = CURRENT_VERSION
```

#### add\_code

```
add_code(
    text: str,
    code_language: Optional[CodeLanguageLabel] = None,
    orig: Optional[str] = None,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
)
```

add\_code.

Parameters:

- text
              (str)
          –
          
str:
- code\_language
              (Optional[CodeLanguageLabel], default:
                  None
)
          –
          
Optional[str]: (Default value = None)
- orig
              (Optional[str], default:
                  None
)
          –
          
Optional[str]:  (Default value = None)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### add\_group

```
add_group(
    label: Optional[GroupLabel] = None,
    name: Optional[str] = None,
    parent: Optional[NodeItem] = None,
) -> GroupItem
```

add\_group.

Parameters:

- label
              (Optional[GroupLabel], default:
                  None
)
          –
          
Optional[GroupLabel]:  (Default value = None)
- name
              (Optional[str], default:
                  None
)
          –
          
Optional[str]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### add\_heading

```
add_heading(
    text: str,
    orig: Optional[str] = None,
    level: LevelNumber = 1,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
)
```

add\_heading.

Parameters:

- label
          –
          
DocItemLabel:
- text
              (str)
          –
          
str:
- orig
              (Optional[str], default:
                  None
)
          –
          
Optional[str]:  (Default value = None)
- level
              (LevelNumber, default:
                  1
)
          –
          
LevelNumber:  (Default value = 1)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### add\_list\_item

```
add_list_item(
    text: str,
    enumerated: bool = False,
    marker: Optional[str] = None,
    orig: Optional[str] = None,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
)
```

add\_list\_item.

Parameters:

- label
          –
          
str:
- text
              (str)
          –
          
str:
- orig
              (Optional[str], default:
                  None
)
          –
          
Optional[str]:  (Default value = None)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### add\_page

```
add_page(
    page_no: int,
    size: Size,
    image: Optional[ImageRef] = None,
) -> PageItem
```

add\_page.

Parameters:

- page\_no
              (int)
          –
          
int:
- size
              (Size)
          –
          
Size:

#### add\_picture

```
add_picture(
    annotations: List[PictureDataType] = [],
    image: Optional[ImageRef] = None,
    caption: Optional[Union[TextItem, RefItem]] = None,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
)
```

add\_picture.

Parameters:

- data
          –
          
List[PictureData]: (Default value = [])
- caption
              (Optional[Union[TextItem, RefItem]], default:
                  None
)
          –
          
Optional[Union[TextItem:
- RefItem]]
          –
          
(Default value = None)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### add\_table

```
add_table(
    data: TableData,
    caption: Optional[Union[TextItem, RefItem]] = None,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
    label: DocItemLabel = TABLE,
)
```

add\_table.

Parameters:

- data
              (TableData)
          –
          
TableData:
- caption
              (Optional[Union[TextItem, RefItem]], default:
                  None
)
          –
          
Optional[Union[TextItem, RefItem]]:  (Default value = None)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)
- label
              (DocItemLabel, default:
                  TABLE
)
          –
          
DocItemLabel:  (Default value = DocItemLabel.TABLE)

#### add\_text

```
add_text(
    label: DocItemLabel,
    text: str,
    orig: Optional[str] = None,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
)
```

add\_text.

Parameters:

- label
              (DocItemLabel)
          –
          
str:
- text
              (str)
          –
          
str:
- orig
              (Optional[str], default:
                  None
)
          –
          
Optional[str]:  (Default value = None)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### add\_title

```
add_title(
    text: str,
    orig: Optional[str] = None,
    prov: Optional[ProvenanceItem] = None,
    parent: Optional[NodeItem] = None,
)
```

add\_title.

Parameters:

- text
              (str)
          –
          
str:
- orig
              (Optional[str], default:
                  None
)
          –
          
Optional[str]:  (Default value = None)
- prov
              (Optional[ProvenanceItem], default:
                  None
)
          –
          
Optional[ProvenanceItem]:  (Default value = None)
- parent
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)

#### check\_version\_is\_compatible

```
check_version_is_compatible(v: str) -> str
```

Check if this document version is compatible with current version.

#### export\_to\_dict

```
export_to_dict(
    mode: str = "json",
    by_alias: bool = True,
    exclude_none: bool = True,
) -> Dict
```

Export to dict.

#### export\_to\_document\_tokens

```
export_to_document_tokens(
    delim: str = "\n",
    from_element: int = 0,
    to_element: int = maxsize,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
    xsize: int = 100,
    ysize: int = 100,
    add_location: bool = True,
    add_content: bool = True,
    add_page_index: bool = True,
    add_table_cell_location: bool = False,
    add_table_cell_label: bool = True,
    add_table_cell_text: bool = True,
    page_no: Optional[int] = None,
    with_groups: bool = True,
    newline: bool = True,
) -> str
```

Exports the document content to a DocumentToken format.

Operates on a slice of the document's body as defined through arguments
from\_element and to\_element; defaulting to the whole main\_text.

Parameters:

- delim
              (str, default:
                  '\n'
)
          –
          
str:  (Default value = "\n\n")
- from\_element
              (int, default:
                  0
)
          –
          
int:  (Default value = 0)
- to\_element
              (int, default:
                  maxsize
)
          –
          
Optional[int]:  (Default value = None)
- labels
              (set[DocItemLabel], default:
                  DEFAULT\_EXPORT\_LABELS
)
          –
          
set[DocItemLabel]
- xsize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- ysize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- add\_location
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_content
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_page\_index
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_table\_cell\_label
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_table\_cell\_text
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)

Returns:

- str
          –
          
The content of the document formatted as a DocTags string.

#### export\_to\_element\_tree

```
export_to_element_tree() -> str
```

Export\_to\_element\_tree.

#### export\_to\_html

```
export_to_html(
    from_element: int = 0,
    to_element: int = maxsize,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
    image_mode: ImageRefMode = PLACEHOLDER,
    formula_to_mathml: bool = True,
    page_no: Optional[int] = None,
    html_lang: str = "en",
    html_head: str = _HTML_DEFAULT_HEAD,
) -> str
```

Serialize to HTML.

#### export\_to\_markdown

```
export_to_markdown(
    delim: str = "\n",
    from_element: int = 0,
    to_element: int = maxsize,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
    strict_text: bool = False,
    escaping_underscores: bool = True,
    image_placeholder: str = "<!-- image -->",
    image_mode: ImageRefMode = PLACEHOLDER,
    indent: int = 4,
    text_width: int = -1,
    page_no: Optional[int] = None,
) -> str
```

Serialize to Markdown.

Operates on a slice of the document's body as defined through arguments
from\_element and to\_element; defaulting to the whole document.

Parameters:

- delim
              (str, default:
                  '\n'
)
          –
          
Delimiter to use when concatenating the various Markdown parts. (Default value = "\n").
- from\_element
              (int, default:
                  0
)
          –
          
Body slicing start index (inclusive). (Default value = 0).
- to\_element
              (int, default:
                  maxsize
)
          –
          
Body slicing stop index (exclusive). (Default value = maxint).
- labels
              (set[DocItemLabel], default:
                  DEFAULT\_EXPORT\_LABELS
)
          –
          
The set of document labels to include in the export.
- strict\_text
              (bool, default:
                  False
)
          –
          
bool: Whether to only include the text content of the document. (Default value = False).
- escaping\_underscores
              (bool, default:
                  True
)
          –
          
bool: Whether to escape underscores in the text content of the document. (Default value = True).
- image\_placeholder
              (str, default:
                  '&lt;!-- image --&gt;'
)
          –
          
The placeholder to include to position images in the markdown. (Default value = "\&lt;!-- image --&gt;").
- image\_mode
              (ImageRefMode, default:
                  PLACEHOLDER
)
          –
          
The mode to use for including images in the markdown. (Default value = ImageRefMode.PLACEHOLDER).
- indent
              (int, default:
                  4
)
          –
          
The indent in spaces of the nested lists. (Default value = 4).

Returns:

- str
          –
          
The exported Markdown representation.

#### export\_to\_text

```
export_to_text(
    delim: str = "\n\n",
    from_element: int = 0,
    to_element: int = 1000000,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
) -> str
```

export\_to\_text.

#### iterate\_items

```
iterate_items(
    root: Optional[NodeItem] = None,
    with_groups: bool = False,
    traverse_pictures: bool = False,
    page_no: Optional[int] = None,
    _level: int = 0,
) -> Iterable[Tuple[NodeItem, int]]
```

iterate\_elements.

Parameters:

- root
              (Optional[NodeItem], default:
                  None
)
          –
          
Optional[NodeItem]:  (Default value = None)
- with\_groups
              (bool, default:
                  False
)
          –
          
bool:  (Default value = False)
- traverse\_pictures
              (bool, default:
                  False
)
          –
          
bool:  (Default value = False)
- page\_no
              (Optional[int], default:
                  None
)
          –
          
Optional[int]:  (Default value = None)
- \_level
              (int, default:
                  0
)
          –
          
(Default value = 0)

#### load\_from\_json

```
load_from_json(filename: Path) -> DoclingDocument
```

load\_from\_json.

Parameters:

- filename
              (Path)
          –
          
The filename to load a saved DoclingDocument from a .json.

Returns:

- DoclingDocument
          –
          
The loaded DoclingDocument.

#### num\_pages

```
num_pages()
```

num\_pages.

#### print\_element\_tree

```
print_element_tree()
```

Print\_element\_tree.

#### save\_as\_document\_tokens

```
save_as_document_tokens(
    filename: Path,
    delim: str = "\n\n",
    from_element: int = 0,
    to_element: int = maxsize,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
    xsize: int = 100,
    ysize: int = 100,
    add_location: bool = True,
    add_content: bool = True,
    add_page_index: bool = True,
    add_table_cell_location: bool = False,
    add_table_cell_label: bool = True,
    add_table_cell_text: bool = True,
    page_no: Optional[int] = None,
    with_groups: bool = True,
)
```

Save the document content to a DocumentToken format.

#### save\_as\_html

```
save_as_html(
    filename: Path,
    artifacts_dir: Optional[Path] = None,
    from_element: int = 0,
    to_element: int = maxsize,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
    image_mode: ImageRefMode = PLACEHOLDER,
    formula_to_mathml: bool = True,
    page_no: Optional[int] = None,
    html_lang: str = "en",
    html_head: str = _HTML_DEFAULT_HEAD,
)
```

Save to HTML.

#### save\_as\_json

```
save_as_json(
    filename: Path,
    artifacts_dir: Optional[Path] = None,
    image_mode: ImageRefMode = EMBEDDED,
    indent: int = 2,
)
```

Save as json.

#### save\_as\_markdown

```
save_as_markdown(
    filename: Path,
    artifacts_dir: Optional[Path] = None,
    delim: str = "\n",
    from_element: int = 0,
    to_element: int = maxsize,
    labels: set[DocItemLabel] = DEFAULT_EXPORT_LABELS,
    strict_text: bool = False,
    escaping_underscores: bool = True,
    image_placeholder: str = "<!-- image -->",
    image_mode: ImageRefMode = PLACEHOLDER,
    indent: int = 4,
    text_width: int = -1,
    page_no: Optional[int] = None,
)
```

Save to markdown.

#### save\_as\_yaml

```
save_as_yaml(
    filename: Path,
    artifacts_dir: Optional[Path] = None,
    image_mode: ImageRefMode = EMBEDDED,
    default_flow_style: bool = False,
)
```

Save as yaml.

#### validate\_document

```
validate_document(d: DoclingDocument)
```

validate\_document.

#### validate\_tree

```
validate_tree(root) -> bool
```

validate\_tree.

### DocumentOrigin

Bases: BaseModel

FileSource.

Methods:

- parse\_hex\_string
            –
            
parse\_hex\_string.
- validate\_mimetype
            –
            
validate\_mimetype.

Attributes:

- binary\_hash
              (Uint64)
          –
- filename
              (str)
          –
- mimetype
              (str)
          –
- uri
              (Optional[AnyUrl])
          –

#### binary\_hash

```
binary_hash: Uint64
```

#### filename

```
filename: str
```

#### mimetype

```
mimetype: str
```

#### uri

```
uri: Optional[AnyUrl] = None
```

#### parse\_hex\_string

```
parse_hex_string(value)
```

parse\_hex\_string.

#### validate\_mimetype

```
validate_mimetype(v)
```

validate\_mimetype.

### DocItem

Bases: NodeItem

DocItem.

Methods:

- get\_image
            –
            
Returns the image of this DocItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- children
              (List[RefItem])
          –
- label
              (DocItemLabel)
          –
- model\_config
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- self\_ref
              (str)
          –

#### children

```
children: List[RefItem] = []
```

#### label

```
label: DocItemLabel
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image of this DocItem.

The function returns None if this DocItem has no valid provenance or
if a valid image of the page containing this DocItem is not available
in doc.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### DocItemLabel

Bases: str, Enum

DocItemLabel.

Methods:

- get\_color
            –
            
Return the RGB color associated with a given label.

Attributes:

- CAPTION
          –
- CHECKBOX\_SELECTED
          –
- CHECKBOX\_UNSELECTED
          –
- CODE
          –
- DOCUMENT\_INDEX
          –
- FOOTNOTE
          –
- FORM
          –
- FORMULA
          –
- KEY\_VALUE\_REGION
          –
- LIST\_ITEM
          –
- PAGE\_FOOTER
          –
- PAGE\_HEADER
          –
- PARAGRAPH
          –
- PICTURE
          –
- REFERENCE
          –
- SECTION\_HEADER
          –
- TABLE
          –
- TEXT
          –
- TITLE
          –

#### CAPTION

```
CAPTION = 'caption'
```

#### CHECKBOX\_SELECTED

```
CHECKBOX_SELECTED = 'checkbox_selected'
```

#### CHECKBOX\_UNSELECTED

```
CHECKBOX_UNSELECTED = 'checkbox_unselected'
```

#### CODE

```
CODE = 'code'
```

#### DOCUMENT\_INDEX

```
DOCUMENT_INDEX = 'document_index'
```

#### FOOTNOTE

```
FOOTNOTE = 'footnote'
```

#### FORM

```
FORM = 'form'
```

#### FORMULA

```
FORMULA = 'formula'
```

#### KEY\_VALUE\_REGION

```
KEY_VALUE_REGION = 'key_value_region'
```

#### LIST\_ITEM

```
LIST_ITEM = 'list_item'
```

#### PAGE\_FOOTER

```
PAGE_FOOTER = 'page_footer'
```

#### PAGE\_HEADER

```
PAGE_HEADER = 'page_header'
```

#### PARAGRAPH

```
PARAGRAPH = 'paragraph'
```

#### PICTURE

```
PICTURE = 'picture'
```

#### REFERENCE

```
REFERENCE = 'reference'
```

#### SECTION\_HEADER

```
SECTION_HEADER = 'section_header'
```

#### TABLE

```
TABLE = 'table'
```

#### TEXT

```
TEXT = 'text'
```

#### TITLE

```
TITLE = 'title'
```

#### get\_color

```
get_color(label: DocItemLabel) -> Tuple[int, int, int]
```

Return the RGB color associated with a given label.

### ProvenanceItem

Bases: BaseModel

ProvenanceItem.

Attributes:

- bbox
              (BoundingBox)
          –
- charspan
              (Tuple[int, int])
          –
- page\_no
              (int)
          –

#### bbox

```
bbox: BoundingBox
```

#### charspan

```
charspan: Tuple[int, int]
```

#### page\_no

```
page_no: int
```

### GroupItem

Bases: NodeItem

GroupItem.

Methods:

- get\_ref
            –
            
get\_ref.

Attributes:

- children
              (List[RefItem])
          –
- label
              (GroupLabel)
          –
- model\_config
          –
- name
              (str)
          –
- parent
              (Optional[RefItem])
          –
- self\_ref
              (str)
          –

#### children

```
children: List[RefItem] = []
```

#### label

```
label: GroupLabel = UNSPECIFIED
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### name

```
name: str = 'group'
```

#### parent

```
parent: Optional[RefItem] = None
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### get\_ref

```
get_ref()
```

get\_ref.

### GroupLabel

Bases: str, Enum

GroupLabel.

Attributes:

- CHAPTER
          –
- COMMENT\_SECTION
          –
- FORM\_AREA
          –
- KEY\_VALUE\_AREA
          –
- LIST
          –
- ORDERED\_LIST
          –
- SECTION
          –
- SHEET
          –
- SLIDE
          –
- UNSPECIFIED
          –

#### CHAPTER

```
CHAPTER = 'chapter'
```

#### COMMENT\_SECTION

```
COMMENT_SECTION = 'comment_section'
```

#### FORM\_AREA

```
FORM_AREA = 'form_area'
```

#### KEY\_VALUE\_AREA

```
KEY_VALUE_AREA = 'key_value_area'
```

#### LIST

```
LIST = 'list'
```

#### ORDERED\_LIST

```
ORDERED_LIST = 'ordered_list'
```

#### SECTION

```
SECTION = 'section'
```

#### SHEET

```
SHEET = 'sheet'
```

#### SLIDE

```
SLIDE = 'slide'
```

#### UNSPECIFIED

```
UNSPECIFIED = 'unspecified'
```

### NodeItem

Bases: BaseModel

NodeItem.

Methods:

- get\_ref
            –
            
get\_ref.

Attributes:

- children
              (List[RefItem])
          –
- model\_config
          –
- parent
              (Optional[RefItem])
          –
- self\_ref
              (str)
          –

#### children

```
children: List[RefItem] = []
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### parent

```
parent: Optional[RefItem] = None
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### get\_ref

```
get_ref()
```

get\_ref.

### PageItem

Bases: BaseModel

PageItem.

Attributes:

- image
              (Optional[ImageRef])
          –
- page\_no
              (int)
          –
- size
              (Size)
          –

#### image

```
image: Optional[ImageRef] = None
```

#### page\_no

```
page_no: int
```

#### size

```
size: Size
```

### FloatingItem

Bases: DocItem

FloatingItem.

Methods:

- caption\_text
            –
            
Computes the caption as a single text.
- get\_image
            –
            
Returns the image corresponding to this FloatingItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- captions
              (List[RefItem])
          –
- children
              (List[RefItem])
          –
- footnotes
              (List[RefItem])
          –
- image
              (Optional[ImageRef])
          –
- label
              (DocItemLabel)
          –
- model\_config
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- references
              (List[RefItem])
          –
- self\_ref
              (str)
          –

#### captions

```
captions: List[RefItem] = []
```

#### children

```
children: List[RefItem] = []
```

#### footnotes

```
footnotes: List[RefItem] = []
```

#### image

```
image: Optional[ImageRef] = None
```

#### label

```
label: DocItemLabel
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### references

```
references: List[RefItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### caption\_text

```
caption_text(doc: DoclingDocument) -> str
```

Computes the caption as a single text.

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image corresponding to this FloatingItem.

This function returns the PIL image from self.image if one is available.
Otherwise, it uses DocItem.get\_image to get an image of this FloatingItem.

In particular, when self.image is None, the function returns None if this
FloatingItem has no valid provenance or the doc does not contain a valid image
for the required page.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### TextItem

Bases: DocItem

TextItem.

Methods:

- export\_to\_document\_tokens
            –
            
Export text element to document tokens format.
- get\_image
            –
            
Returns the image of this DocItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- children
              (List[RefItem])
          –
- label
              (Literal[CAPTION, CHECKBOX\_SELECTED, CHECKBOX\_UNSELECTED, FOOTNOTE, FORMULA, PAGE\_FOOTER, PAGE\_HEADER, PARAGRAPH, REFERENCE, TEXT, TITLE])
          –
- model\_config
          –
- orig
              (str)
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- self\_ref
              (str)
          –
- text
              (str)
          –

#### children

```
children: List[RefItem] = []
```

#### label

```
label: Literal[
    CAPTION,
    CHECKBOX_SELECTED,
    CHECKBOX_UNSELECTED,
    FOOTNOTE,
    FORMULA,
    PAGE_FOOTER,
    PAGE_HEADER,
    PARAGRAPH,
    REFERENCE,
    TEXT,
    TITLE,
]
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### orig

```
orig: str
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### text

```
text: str
```

#### export\_to\_document\_tokens

```
export_to_document_tokens(
    doc: DoclingDocument,
    new_line: str = "\n",
    xsize: int = 100,
    ysize: int = 100,
    add_location: bool = True,
    add_content: bool = True,
    add_page_index: bool = True,
)
```

Export text element to document tokens format.

Parameters:

- doc
              (DoclingDocument)
          –
          
"DoclingDocument":
- new\_line
              (str, default:
                  '\n'
)
          –
          
str:  (Default value = "\n")
- xsize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- ysize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- add\_location
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_content
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_page\_index
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image of this DocItem.

The function returns None if this DocItem has no valid provenance or
if a valid image of the page containing this DocItem is not available
in doc.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### TableItem

Bases: FloatingItem

TableItem.

Methods:

- caption\_text
            –
            
Computes the caption as a single text.
- export\_to\_dataframe
            –
            
Export the table as a Pandas DataFrame.
- export\_to\_document\_tokens
            –
            
Export table to document tokens format.
- export\_to\_html
            –
            
Export the table as html.
- export\_to\_markdown
            –
            
Export the table as markdown.
- export\_to\_otsl
            –
            
Export the table as OTSL.
- get\_image
            –
            
Returns the image corresponding to this FloatingItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- captions
              (List[RefItem])
          –
- children
              (List[RefItem])
          –
- data
              (TableData)
          –
- footnotes
              (List[RefItem])
          –
- image
              (Optional[ImageRef])
          –
- label
              (Literal[DOCUMENT\_INDEX, TABLE])
          –
- model\_config
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- references
              (List[RefItem])
          –
- self\_ref
              (str)
          –

#### captions

```
captions: List[RefItem] = []
```

#### children

```
children: List[RefItem] = []
```

#### data

```
data: TableData
```

#### footnotes

```
footnotes: List[RefItem] = []
```

#### image

```
image: Optional[ImageRef] = None
```

#### label

```
label: Literal[DOCUMENT_INDEX, TABLE] = TABLE
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### references

```
references: List[RefItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### caption\_text

```
caption_text(doc: DoclingDocument) -> str
```

Computes the caption as a single text.

#### export\_to\_dataframe

```
export_to_dataframe() -> DataFrame
```

Export the table as a Pandas DataFrame.

#### export\_to\_document\_tokens

```
export_to_document_tokens(
    doc: DoclingDocument,
    new_line: str = "\n",
    xsize: int = 100,
    ysize: int = 100,
    add_location: bool = True,
    add_caption: bool = True,
    add_content: bool = True,
    add_cell_location: bool = True,
    add_cell_label: bool = True,
    add_cell_text: bool = True,
    add_page_index: bool = True,
)
```

Export table to document tokens format.

Parameters:

- doc
              (DoclingDocument)
          –
          
"DoclingDocument":
- new\_line
              (str, default:
                  '\n'
)
          –
          
str:  (Default value = "\n")
- xsize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- ysize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- add\_location
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_caption
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_content
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_cell\_location
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_cell\_label
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_cell\_text
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_page\_index
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)

#### export\_to\_html

```
export_to_html(
    doc: Optional[DoclingDocument] = None,
    add_caption: bool = True,
) -> str
```

Export the table as html.

#### export\_to\_markdown

```
export_to_markdown() -> str
```

Export the table as markdown.

#### export\_to\_otsl

```
export_to_otsl(
    doc: DoclingDocument,
    add_cell_location: bool = True,
    add_cell_text: bool = True,
    xsize: int = 100,
    ysize: int = 100,
) -> str
```

Export the table as OTSL.

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image corresponding to this FloatingItem.

This function returns the PIL image from self.image if one is available.
Otherwise, it uses DocItem.get\_image to get an image of this FloatingItem.

In particular, when self.image is None, the function returns None if this
FloatingItem has no valid provenance or the doc does not contain a valid image
for the required page.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### TableCell

Bases: BaseModel

TableCell.

Methods:

- from\_dict\_format
            –
            
from\_dict\_format.

Attributes:

- bbox
              (Optional[BoundingBox])
          –
- col\_span
              (int)
          –
- column\_header
              (bool)
          –
- end\_col\_offset\_idx
              (int)
          –
- end\_row\_offset\_idx
              (int)
          –
- row\_header
              (bool)
          –
- row\_section
              (bool)
          –
- row\_span
              (int)
          –
- start\_col\_offset\_idx
              (int)
          –
- start\_row\_offset\_idx
              (int)
          –
- text
              (str)
          –

#### bbox

```
bbox: Optional[BoundingBox] = None
```

#### col\_span

```
col_span: int = 1
```

#### column\_header

```
column_header: bool = False
```

#### end\_col\_offset\_idx

```
end_col_offset_idx: int
```

#### end\_row\_offset\_idx

```
end_row_offset_idx: int
```

#### row\_header

```
row_header: bool = False
```

#### row\_section

```
row_section: bool = False
```

#### row\_span

```
row_span: int = 1
```

#### start\_col\_offset\_idx

```
start_col_offset_idx: int
```

#### start\_row\_offset\_idx

```
start_row_offset_idx: int
```

#### text

```
text: str
```

#### from\_dict\_format

```
from_dict_format(data: Any) -> Any
```

from\_dict\_format.

### TableData

Bases: BaseModel

BaseTableData.

Attributes:

- grid
              (List[List[TableCell]])
          –
          
grid.
- num\_cols
              (int)
          –
- num\_rows
              (int)
          –
- table\_cells
              (List[TableCell])
          –

#### grid

```
grid: List[List[TableCell]]
```

grid.

#### num\_cols

```
num_cols: int = 0
```

#### num\_rows

```
num_rows: int = 0
```

#### table\_cells

```
table_cells: List[TableCell] = []
```

### TableCellLabel

Bases: str, Enum

TableCellLabel.

Attributes:

- BODY
          –
- COLUMN\_HEADER
          –
- ROW\_HEADER
          –
- ROW\_SECTION
          –

#### BODY

```
BODY = 'body'
```

#### COLUMN\_HEADER

```
COLUMN_HEADER = 'col_header'
```

#### ROW\_HEADER

```
ROW_HEADER = 'row_header'
```

#### ROW\_SECTION

```
ROW_SECTION = 'row_section'
```

### KeyValueItem

Bases: DocItem

KeyValueItem.

Methods:

- get\_image
            –
            
Returns the image of this DocItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- children
              (List[RefItem])
          –
- label
              (Literal[KEY\_VALUE\_REGION])
          –
- model\_config
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- self\_ref
              (str)
          –

#### children

```
children: List[RefItem] = []
```

#### label

```
label: Literal[KEY_VALUE_REGION] = KEY_VALUE_REGION
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image of this DocItem.

The function returns None if this DocItem has no valid provenance or
if a valid image of the page containing this DocItem is not available
in doc.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### SectionHeaderItem

Bases: TextItem

SectionItem.

Methods:

- export\_to\_document\_tokens
            –
            
Export text element to document tokens format.
- get\_image
            –
            
Returns the image of this DocItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- children
              (List[RefItem])
          –
- label
              (Literal[SECTION\_HEADER])
          –
- level
              (LevelNumber)
          –
- model\_config
          –
- orig
              (str)
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- self\_ref
              (str)
          –
- text
              (str)
          –

#### children

```
children: List[RefItem] = []
```

#### label

```
label: Literal[SECTION_HEADER] = SECTION_HEADER
```

#### level

```
level: LevelNumber = 1
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### orig

```
orig: str
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### text

```
text: str
```

#### export\_to\_document\_tokens

```
export_to_document_tokens(
    doc: DoclingDocument,
    new_line: str = "\n",
    xsize: int = 100,
    ysize: int = 100,
    add_location: bool = True,
    add_content: bool = True,
    add_page_index: bool = True,
)
```

Export text element to document tokens format.

Parameters:

- doc
              (DoclingDocument)
          –
          
"DoclingDocument":
- new\_line
              (str, default:
                  '\n'
)
          –
          
str:  (Default value = "\n")
- xsize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- ysize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- add\_location
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_content
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_page\_index
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image of this DocItem.

The function returns None if this DocItem has no valid provenance or
if a valid image of the page containing this DocItem is not available
in doc.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### PictureItem

Bases: FloatingItem

PictureItem.

Methods:

- caption\_text
            –
            
Computes the caption as a single text.
- export\_to\_document\_tokens
            –
            
Export picture to document tokens format.
- export\_to\_html
            –
            
Export picture to HTML format.
- export\_to\_markdown
            –
            
Export picture to Markdown format.
- get\_image
            –
            
Returns the image corresponding to this FloatingItem.
- get\_location\_tokens
            –
            
Get the location string for the BaseCell.
- get\_ref
            –
            
get\_ref.

Attributes:

- annotations
              (List[PictureDataType])
          –
- captions
              (List[RefItem])
          –
- children
              (List[RefItem])
          –
- footnotes
              (List[RefItem])
          –
- image
              (Optional[ImageRef])
          –
- label
              (Literal[PICTURE])
          –
- model\_config
          –
- parent
              (Optional[RefItem])
          –
- prov
              (List[ProvenanceItem])
          –
- references
              (List[RefItem])
          –
- self\_ref
              (str)
          –

#### annotations

```
annotations: List[PictureDataType] = []
```

#### captions

```
captions: List[RefItem] = []
```

#### children

```
children: List[RefItem] = []
```

#### footnotes

```
footnotes: List[RefItem] = []
```

#### image

```
image: Optional[ImageRef] = None
```

#### label

```
label: Literal[PICTURE] = PICTURE
```

#### model\_config

```
model_config = ConfigDict(extra='forbid')
```

#### parent

```
parent: Optional[RefItem] = None
```

#### prov

```
prov: List[ProvenanceItem] = []
```

#### references

```
references: List[RefItem] = []
```

#### self\_ref

```
self_ref: str = Field(pattern=_JSON_POINTER_REGEX)
```

#### caption\_text

```
caption_text(doc: DoclingDocument) -> str
```

Computes the caption as a single text.

#### export\_to\_document\_tokens

```
export_to_document_tokens(
    doc: DoclingDocument,
    new_line: str = "\n",
    xsize: int = 100,
    ysize: int = 100,
    add_location: bool = True,
    add_caption: bool = True,
    add_content: bool = True,
    add_page_index: bool = True,
)
```

Export picture to document tokens format.

Parameters:

- doc
              (DoclingDocument)
          –
          
"DoclingDocument":
- new\_line
              (str, default:
                  '\n'
)
          –
          
str:  (Default value = "\n")
- xsize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- ysize
              (int, default:
                  100
)
          –
          
int:  (Default value = 100)
- add\_location
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_caption
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)
- add\_content
              (bool, default:
                  True
)
          –
          
bool:  (Default value = True)

#### export\_to\_html

```
export_to_html(
    doc: DoclingDocument,
    add_caption: bool = True,
    image_mode: ImageRefMode = PLACEHOLDER,
) -> str
```

Export picture to HTML format.

#### export\_to\_markdown

```
export_to_markdown(
    doc: DoclingDocument,
    add_caption: bool = True,
    image_mode: ImageRefMode = EMBEDDED,
    image_placeholder: str = "<!-- image -->",
) -> str
```

Export picture to Markdown format.

#### get\_image

```
get_image(doc: DoclingDocument) -> Optional[Image]
```

Returns the image corresponding to this FloatingItem.

This function returns the PIL image from self.image if one is available.
Otherwise, it uses DocItem.get\_image to get an image of this FloatingItem.

In particular, when self.image is None, the function returns None if this
FloatingItem has no valid provenance or the doc does not contain a valid image
for the required page.

#### get\_location\_tokens

```
get_location_tokens(
    doc: DoclingDocument,
    new_line: str,
    xsize: int = 100,
    ysize: int = 100,
    add_page_index: bool = True,
) -> str
```

Get the location string for the BaseCell.

#### get\_ref

```
get_ref()
```

get\_ref.

### ImageRef

Bases: BaseModel

ImageRef.

Methods:

- from\_pil
            –
            
Construct ImageRef from a PIL Image.
- validate\_mimetype
            –
            
validate\_mimetype.

Attributes:

- dpi
              (int)
          –
- mimetype
              (str)
          –
- pil\_image
              (Optional[Image])
          –
          
Return the PIL Image.
- size
              (Size)
          –
- uri
              (Union[AnyUrl, Path])
          –

#### dpi

```
dpi: int
```

#### mimetype

```
mimetype: str
```

#### pil\_image

```
pil_image: Optional[Image]
```

Return the PIL Image.

#### size

```
size: Size
```

#### uri

```
uri: Union[AnyUrl, Path] = Field(union_mode="left_to_right")
```

#### from\_pil

```
from_pil(image: Image, dpi: int) -> Self
```

Construct ImageRef from a PIL Image.

#### validate\_mimetype

```
validate_mimetype(v)
```

validate\_mimetype.

### PictureClassificationClass

Bases: BaseModel

PictureClassificationData.

Attributes:

- class\_name
              (str)
          –
- confidence
              (float)
          –

#### class\_name

```
class_name: str
```

#### confidence

```
confidence: float
```

### PictureClassificationData

Bases: BasePictureData

PictureClassificationData.

Attributes:

- kind
              (Literal['classification'])
          –
- predicted\_classes
              (List[PictureClassificationClass])
          –
- provenance
              (str)
          –

#### kind

```
kind: Literal['classification'] = 'classification'
```

#### predicted\_classes

```
predicted_classes: List[PictureClassificationClass]
```

#### provenance

```
provenance: str
```

### RefItem

Bases: BaseModel

RefItem.

Methods:

- get\_ref
            –
            
get\_ref.
- resolve
            –
            
resolve.

Attributes:

- cref
              (str)
          –
- model\_config
          –

#### cref

```
cref: str = Field(alias="$ref", pattern=_JSON_POINTER_REGEX)
```

#### model\_config

```
model_config = ConfigDict(populate_by_name=True)
```

#### get\_ref

```
get_ref()
```

get\_ref.

#### resolve

```
resolve(doc: DoclingDocument)
```

resolve.

### BoundingBox

Bases: BaseModel

BoundingBox.

Methods:

- area
            –
            
area.
- as\_tuple
            –
            
as\_tuple.
- expand\_by\_scale
            –
            
expand\_to\_size.
- from\_tuple
            –
            
from\_tuple.
- intersection\_area\_with
            –
            
Calculate the intersection area with another bounding box.
- intersection\_over\_self
            –
            
intersection\_over\_self.
- intersection\_over\_union
            –
            
intersection\_over\_union.
- is\_above
            –
            
is\_above.
- is\_horizontally\_connected
            –
            
is\_horizontally\_connected.
- is\_left\_of
            –
            
is\_left\_of.
- is\_strictly\_above
            –
            
is\_strictly\_above.
- is\_strictly\_left\_of
            –
            
is\_strictly\_left\_of.
- normalized
            –
            
normalized.
- overlaps
            –
            
overlaps.
- overlaps\_horizontally
            –
            
Check if two bounding boxes overlap horizontally.
- overlaps\_vertically
            –
            
Check if two bounding boxes overlap vertically.
- overlaps\_vertically\_with\_iou
            –
            
overlaps\_y\_with\_iou.
- resize\_by\_scale
            –
            
resize\_by\_scale.
- scale\_to\_size
            –
            
scale\_to\_size.
- scaled
            –
            
scaled.
- to\_bottom\_left\_origin
            –
            
to\_bottom\_left\_origin.
- to\_top\_left\_origin
            –
            
to\_top\_left\_origin.

Attributes:

- b
              (float)
          –
- coord\_origin
              (CoordOrigin)
          –
- height
          –
          
height.
- l
              (float)
          –
- r
              (float)
          –
- t
              (float)
          –
- width
          –
          
width.

#### b

```
b: float
```

#### coord\_origin

```
coord_origin: CoordOrigin = TOPLEFT
```

#### height

```
height
```

height.

#### l

```
l: float
```

#### r

```
r: float
```

#### t

```
t: float
```

#### width

```
width
```

width.

#### area

```
area() -> float
```

area.

#### as\_tuple

```
as_tuple() -> Tuple[float, float, float, float]
```

as\_tuple.

#### expand\_by\_scale

```
expand_by_scale(
    x_scale: float, y_scale: float
) -> BoundingBox
```

expand\_to\_size.

#### from\_tuple

```
from_tuple(coord: Tuple[float, ...], origin: CoordOrigin)
```

from\_tuple.

Parameters:

- coord
              (Tuple[float, ...])
          –
          
Tuple[float:
- ...]
          –
- origin
              (CoordOrigin)
          –
          
CoordOrigin:

#### intersection\_area\_with

```
intersection_area_with(other: BoundingBox) -> float
```

Calculate the intersection area with another bounding box.

#### intersection\_over\_self

```
intersection_over_self(
    other: BoundingBox, eps: float = 1e-06
) -> float
```

intersection\_over\_self.

#### intersection\_over\_union

```
intersection_over_union(
    other: BoundingBox, eps: float = 1e-06
) -> float
```

intersection\_over\_union.

#### is\_above

```
is_above(other: BoundingBox) -> bool
```

is\_above.

#### is\_horizontally\_connected

```
is_horizontally_connected(
    elem_i: BoundingBox, elem_j: BoundingBox
) -> bool
```

is\_horizontally\_connected.

#### is\_left\_of

```
is_left_of(other: BoundingBox) -> bool
```

is\_left\_of.

#### is\_strictly\_above

```
is_strictly_above(
    other: BoundingBox, eps: float = 0.001
) -> bool
```

is\_strictly\_above.

#### is\_strictly\_left\_of

```
is_strictly_left_of(
    other: BoundingBox, eps: float = 0.001
) -> bool
```

is\_strictly\_left\_of.

#### normalized

```
normalized(page_size: Size)
```

normalized.

#### overlaps

```
overlaps(other: BoundingBox) -> bool
```

overlaps.

#### overlaps\_horizontally

```
overlaps_horizontally(other: BoundingBox) -> bool
```

Check if two bounding boxes overlap horizontally.

#### overlaps\_vertically

```
overlaps_vertically(other: BoundingBox) -> bool
```

Check if two bounding boxes overlap vertically.

#### overlaps\_vertically\_with\_iou

```
overlaps_vertically_with_iou(
    other: BoundingBox, iou: float
) -> bool
```

overlaps\_y\_with\_iou.

#### resize\_by\_scale

```
resize_by_scale(x_scale: float, y_scale: float)
```

resize\_by\_scale.

#### scale\_to\_size

```
scale_to_size(old_size: Size, new_size: Size)
```

scale\_to\_size.

#### scaled

```
scaled(scale: float)
```

scaled.

#### to\_bottom\_left\_origin

```
to_bottom_left_origin(page_height: float) -> BoundingBox
```

to\_bottom\_left\_origin.

Parameters:

- page\_height
              (float)
          –

#### to\_top\_left\_origin

```
to_top_left_origin(page_height: float) -> BoundingBox
```

to\_top\_left\_origin.

Parameters:

- page\_height
              (float)
          –

### CoordOrigin

Bases: str, Enum

CoordOrigin.

Attributes:

- BOTTOMLEFT
          –
- TOPLEFT
          –

#### BOTTOMLEFT

```
BOTTOMLEFT = 'BOTTOMLEFT'
```

#### TOPLEFT

```
TOPLEFT = 'TOPLEFT'
```

### ImageRefMode

Bases: str, Enum

ImageRefMode.

Attributes:

- EMBEDDED
          –
- PLACEHOLDER
          –
- REFERENCED
          –

#### EMBEDDED

```
EMBEDDED = 'embedded'
```

#### PLACEHOLDER

```
PLACEHOLDER = 'placeholder'
```

#### REFERENCED

```
REFERENCED = 'referenced'
```

### Size

Bases: BaseModel

Size.

Methods:

- as\_tuple
            –
            
as\_tuple.

Attributes:

- height
              (float)
          –
- width
              (float)
          –

#### height

```
height: float = 0.0
```

#### width

```
width: float = 0.0
```

#### as\_tuple

```
as_tuple()
```

as\_tuple.