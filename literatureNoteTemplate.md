---
citekey: {{citekey}}
doi: "{{DOI}}"
title: "{{title}}"
authors: {{authors}}
tags: [literatureNote, {% for t in tags %}{{t.tag}}{% if not loop.last %}, {% endif %}{% endfor %}]
collection: "{{collections[0].name}}"
year: {{date | format("YYYY")}}
noteType: literatureNote
status: 
summary:
parameterx:
---

## [{{title}}]({{desktopURI}})

{% persist "notes" %}
{% if isFirstImport %}

### Key takeaways



{% endif %}{% endpersist %}

> [!info]- Info - [**Zotero**]({{desktopURI}}) | [**DOI**](https://doi.org/{{DOI}}) | {% for attachment in attachments | filterby("path", "endswith", ".pdf") %}[**PDF**](file:///{{attachment.path | replace(" ", "%20")}}){%- endfor %}
>
> {% if bibliography %}**Bibliography**: {{bibliography|replace("\n"," ")}}{% endif %}
> 
> **Authors**:: {% for a in creators %} [[{{a.firstName}} {{a.lastName}}]]{% if not loop.last %}, {% endif %}{% endfor %}
> 
> {% if hashTags %}**Tags**: {{hashTags}}{% endif %}
> 
> **Collections**:: {% for collection in collections %}[[{{collection.name}}]]{% if not loop.last %}, {% endif %}{% endfor %}
> 
> **First-page**: {% for annotation in annotations %}{% if loop.first %}{{annotation.pageLabel}}{% endif %}{% endfor %}

 
---

## Reading notes
{% macro heading(color) -%}
{%- if color == "#ffd400" -%}
⭐ Interesting
{%- endif -%}
{%- if color == "#2ea8e5" -%}
💡 Key claim
{%- endif -%}
{%- if color == "#e56eee" -%}
📈 Results / evidence
{%- endif -%}
{%- if color == "#f19837" -%}
📋 Methods
{%- endif -%}
{%- if color == "#5fb236" -%}
🖊️ Quote
{%- endif -%}
{%- if color == "#ff6666" -%}
❓Confusing
{%- endif -%}
{%- if color == "#aaaaaa" -%}
🔤 Definition
{%- endif -%}
{%- if color == "#a28ae5" -%}
🧩 New reference 
{%- endif -%}
{%- endmacro -%}

{% macro calloutCharacter(color) -%}
{%- if color == "#5fb236" -%}$
{%- elif color == "#2ea8e5" -%}@
{%- elif color == "#ffd400" -%}&
{%- elif color == "#a28ae5" -%}~
{%- elif color == "#ff6666" -%}!
{%- elif color == "#e56eee" -%}€
{%- elif color == "#f19837" -%}?
{%- elif color == "#aaaaaa" -%}%
{%- else -%}•
{%- endif -%}
{%- endmacro %}

{% persist "annotations" %}
{% set annotations = annotations | filterby("date", "dateafter", lastImportDate) -%}
{% if annotations.length > 0 %}
*Imported on {{importDate | format("YYYY-MM-DD HH:mm")}}*

{% for color, annotations in annotations | groupby("color") -%}

### {{heading(color)}} 
{% for annotation in annotations -%}
{%- if annotation.imageRelativePath %}

> [!cite]+ Image [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}})
> ![[{{annotation.imageRelativePath}}]]{% if annotation.hashTags %}
> {{annotation.hashTags}}{% endif %}{%- if (annotation.comment or []).indexOf("todo ") !== -1 %}
> - [x] **{{annotation.comment | replace("todo ", "")}}**{% else %} ✅ 2024-05-28
> **{{annotation.comment}}**{%- endif -%}

{% elif (annotation.comment or []).indexOf("todo ") !== -1 %}
- [x] **{{annotation.comment | replace("todo ", "")}}**:{% if not annotation.annotatedText %} [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}}){% else %} ✅ 2024-05-28
	- {{calloutCharacter(annotation.color)}} {{annotation.annotatedText | nl2br}} [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}}) {% if annotation.hashTags %}{{annotation.hashTags}}{% endif -%}{% endif -%}
{% elif annotation.comment %}
- **{{annotation.comment}}**:{% if not annotation.annotatedText %} [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}}){% else %}
	- {{calloutCharacter(annotation.color)}} {{annotation.annotatedText | nl2br}} [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}}) {% if annotation.hashTags %}{{annotation.hashTags}}{% endif -%}{% endif %}
{%- elif annotation.annotatedText %}
- {{calloutCharacter(annotation.color)}} {{annotation.annotatedText | nl2br}} [(p. {{annotation.pageLabel}})](zotero://open-pdf/library/items/{{annotation.attachment.itemKey}}?page={{annotation.pageLabel}}&annotation={{annotation.id}}) {% if annotation.hashTags %}{{annotation.hashTags}}{% endif %}
{%- endif -%}{%- endfor %}

{% endfor -%}
{% endif %}
{% endpersist %}
