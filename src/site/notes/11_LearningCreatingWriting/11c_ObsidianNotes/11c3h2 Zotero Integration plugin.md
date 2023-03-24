---
{"dg-publish":true,"permalink":"/11-learning-creating-writing/11c-obsidian-notes/11c3h2-zotero-integration-plugin/"}
---

# Zotero Integration plugin links Zotero to Obsidian for literature notes and citations

To configure this plugin first download and enable the Zotero Integration plugin for Obsidian. Also download and install the BetterBibtext plugin for Zotero. 

After enabling Zotero Integration, adjust its settings to enable its two main features: Insert citation into a note and creating a note based on Zotero data including highlighted text.
Once configured you should be able to choose the Zotero Integration command from the command palette (Command P), which should activate the Zotero search bar. Selecting a reference from the Zotero search should produce either a citation in the current note or create a literature note.

The resulting literature note should look something like this:
![11c3h2 Zotero Integration plugin_image_1.png|600](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_1.png)

The resulting literature notes can be tabulated, sorted, and filtered with dataview
[[11_LearningCreatingWriting/11c_ObsidianNotes/11c3h2a Dataview for literature note summary\|11c3h2a Dataview for literature note summary]]

## Configure Zotero Integration settings for citations and literature notes
The Zotero Integration plugin can be configured to insert citation information into a note, such as an article bibliography reference or a hyperlink to a Zotero entry.  It can also be configure to insert a new note containing meta data and highlighted text.

### Configure to insert citation information into a note

Configure to insert a citation key link to a reference in Zotero.  This is useful when you just want to add a citation and connection to a Zotero reference without creating an entire literature note for the reference.
![11c3h2 Zotero Integration plugin_image_2.png|600](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_2.png)
This will insert a hyperlink in a note that connects to the Zotero reference:
[Lee2004](zotero://select/library/items/JCW7FKYC)

Configure to insert a . 
![11c3h2 Zotero Integration plugin_image_2.png](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_2.png)
Make sure that Zotero has the Bibliography Style that matches the Bibliography Style selected in Obsidian.
This will insert a bibliographic entry into a note:
Lee, J. D., & See, K. A. (2004). Trust in automation: Designing for appropriate reliance. _Human Factors_, _46_(1), 50–80.


### Configure to create a literature note with metadata and annotations
![11c3h2 Zotero Integration plugin_image_3.png|600](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_3.png)
[[LiteratureNotes/@Lee2004\|@Lee2004]]


## Create the literature note template referenced by the Zotero Integration settings
Create a template and save it to your template directory specified in the Zotero Integration settings.  Like other templates, this is just a markdown file with some text that is included when you create a note based on the template.  It also has some code that extracts information from the Zotero database. 
The literature note this template produces consists of YAML front matter and note content. The YAML data and key-value lines in the note can be queried by dataview. In the YAML front matter the key-value pairs are separated by a single colon, in the note they are separated by two colons.
- YAML front matter
	- Metadata is hidden when in reader view and reduces clutter when viewing note
	- cssclass makes it possible to assign formatting to the identified note type
	- Metadata includes a key separated by the values by a colon. 
- Note content
	- `persist` statements keep the elements of the notes between these statements form being changed when new annotations are added from Obsidian
	- Zotero data fields `{{...}}` are filled when template is used to create a note 
	- Nunchucks code can extract and organize annotations as demonstrated by how annotation color can organize note content
	- The `color` specifies the hexcode of the highlight color from Zotero.  This can be changed to 'colorCategory' to be more compatible with annotations from PDF annotation tools other than Zotero.
	- Colons in the YAML beyond those separating the key from the value create errors. Place quotes around title and abstract to avoid this problem: "..."
- 
```
---
cssclass: literature-note
title: "{{title}}"
authors: {{authors}}
year: {{date | format(”Y”)}}
publication: {{publicationTitle}}
abstract: "{{abstractNote}}"
---
#lit_note
[{{citationKey}}]({{desktopURI}})
{{bibliography}}

{% persist "notes" %}

## Review
type::
topic::
contribution::
evidence::
limitations::
to read::
comments::

## Connections
support::
contradict::
from::
to::

## Related notes

{% endpersist %}

## Annotations
{% macro calloutHeader(color) -%}
{%- if color == "#a28ae5" -%}
Highlight 
{%- endif -%}
{%- if color == "#2ea8e5" -%}
Reference to read 
{%- endif -%}
{%- if color == "#5fb236" -%}
Evidence
{%- endif -%}
{%- if color == "#ff6666" -%}
Limitations
{%- endif -%}
{%- if color == "#ffd400" -%}
Contribution 
{%- endif -%}
{%- endmacro -%}

{% persist "annotations" %}
{% set annotations = annotations | filterby("date", "dateafter", lastImportDate) -%}
{% if annotations.length > 0 %}

#### Imported on {{importDate | format("YYYY-MM-DD h:mm a")}}

{% for color, annotations in annotations | groupby("color") -%}

> [!quote|{{color}}] {{calloutHeader(color)}}
> {%- for annotation in annotations -%}
{%- if annotation.imageRelativePath %}
> ![[{{annotation.imageRelativePath}}]]
{%- endif %}
{%- if annotation.annotatedText %}
>{{annotation.annotatedText}} [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}}){%- endif -%}
{%- if annotation.comment %}
>
>> {{annotation.comment | nl2br}}{%- endif -%}  <br>
{%- endfor %}

{% endfor -%}
{% endif %}
{% endpersist %}

```

## Add a CSS file to the `snippets` folder  to format callout box color and icons
The CSS snippets make it possible to change how notes are formatted and the YAML frontmatter `cssclass: literature-note` identifies that literature notes should be formatted differently. The ''.literature-note." in the CSS snippet must match the "cssclass: literature-note"  in the YAML frontmatter. Create a text file with a ".css" extension with the following contents. 

Put the .css snippet file into the "snippets" folder.  This is a hidden folder in the Obsidian Vault folder that can be opened from the folder icon shortcut (Appearance -> scroll down to CSS snippets). The ‘literature-note.css’ should be stored here.Do not need store in the main folder or template folder—all .css snippets go here.
![11c3h2 Zotero Integration plugin_image_4.png](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_4.png)
Lucide icons can be used to customize the callouts and are included in the snippet code to demonstrate how this works.
[Lucide](https://lucide.dev)
![11c3h2 Zotero Integration plugin_image_5.png|500](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_5.png)

The following code tailors the color of the callout to the color used to highlight text in Zotero. It also uses a custom icon based on the Lucide library.
```
/* Yellow */
.literature-note .callout[data-callout-metadata="#ffd400"] {
  --callout-color: 255, 204, 0;
  --callout-icon: lucide-highlighter;
}

/* Red */
.literature-note .callout[data-callout-metadata="#ff6666"] {
  --callout-color: 255, 59, 48;
  --callout-icon: lucide-highlighter;
}

/* Green */
.literature-note .callout[data-callout-metadata="#5fb236"] {
  --callout-color: 40, 205, 65;
  --callout-icon: lucide-highlighter;
}

/* Blue */
.literature-note .callout[data-callout-metadata="#2ea8e5"] {
  --callout-color: 0, 122, 255;
  --callout-icon: lucide-highlighter;
}

/* Purple */
.literature-note .callout[data-callout-metadata="#a28ae5"] {
  --callout-color: 125, 84, 222;
  --callout-icon: lucide-highlighter;
}
```

## Use highlight colors to  organize annotations in Zotero
When using Zotero to review papers use different colors to highlight passages.  This encouraged a greater depth of processing and helps the future you quickly orient to why certain passages merited highlighting. When highlighting in Zotero the colors of these highlights will be extracted, labeled, and colored in the literature note. Highlights with other PDF readers might not match the Zotero colors and the template will fail to match them to the categories.
- <mark style="background: #FFF3A3A6;">Yellow~Contributions</mark>
- <mark style="background: #BBFABBA6;">Green~Evidence</mark>
- <mark style="background: #FF5582A6;">Red~Limitations </mark>
- <mark style="background: #ADCCFFA6;">Blue~References to read</mark>

## Zotero Data explorer to identify fields for template
There are many other data elements that could be included in the template. The Zotero integraton: Data explorer command shows some of these.
![11c3h2 Zotero Integration plugin_image_6.png|400](/img/user/Attachments/11c3h2%20Zotero%20Integration%20plugin_image_6.png)


---
### Date:: [[ 2023-03-19 \| 2023-03-19 ]]
### Revisit::
### Links

### Tags
### Context
- [[11_LearningCreatingWriting/11c3h Execute code plugin\|11c3h Execute code plugin]]
- [[11_LearningCreatingWriting/11c_ObsidianNotes/11c3h Zotero integration\|11c3h Zotero integration]]
- [[11_LearningCreatingWriting/11c_ObsidianNotes/11c3h2a Dataview for literature note summary\|11c3h2a Dataview for literature note summary]]


### References
