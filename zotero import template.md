---
citekey: {{citekey}}
aliases: ["
    {%- if creators -%}
        {{creators[0].lastName}}
        {%- if creators|length > 1 %} et al.{% endif -%}
    {%- endif -%}
    {%- if date %} ({{date | format("YYYY")}}){% endif -%} 
    {%- if shortTitle %} {{shortTitle | safe}} {%- else %} {{title | safe}} {%- endif -%}
"]
doi: "{{DOI}}"
title: "{{title}}"
authors: {{authors}}
tags: [literatureNote, {% for t in tags %}{{t.tag}}{% if not loop.last %}, {% endif %}{% endfor %}]
year: {{date | format("YYYY")}}
status: 
referenceTrail: open
collection: "{{collections[0].name}}"
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

> [!abstract]-
> {% if abstractNote %}
> {{abstractNote|replace("\n"," ")|striptags(true)|replace("Objectives", "**Objectives**")|replace("Background", "**Background**")|replace("Methodology", "**Methodology**")|replace("Results","**Results**")|replace("Conclusion","**Conclusion**")}}
> {% endif %}

 
---

## Reading notes
{% macro heading(color) -%}
{%- if color == "#ffd400" -%}
⭐ Important
{%- endif -%}
{%- if color == "#2ea8e5" -%}
💡 Potential reference 
{%- endif -%}
{%- if color == "#e56eee" -%}
📈 Results
{%- endif -%}
{%- if color == "#f19837" -%}
📋 Methods
{%- endif -%}
{%- if color == "#5fb236" -%}
🖊️ Quote
{%- endif -%}
{%- if color == "#ff6666" -%}
⛔ Disagree
{%- endif -%}
{%- if color == "#aaaaaa" -%}
❓ Confusing
{%- endif -%}
{%- if color == "#a28ae5" -%}
🧩 New reference 
{%- endif -%}
{%- endmacro -%}

{% macro calloutCharacter(color) -%}
{%- if color == "#ffd400" -%}
&
{%- endif -%}
{%- if color == "#2ea8e5" -%}
@
{%- endif -%}
{%- if color == "#e56eee" -%}
\>
{%- endif -%}
{%- if color == "#f19837" -%}
§
{%- endif -%}
{%- if color == "#5fb236" -%}
$
{%- endif -%}
{%- if color == "#ff6666" -%}
!
{%- endif -%}
{%- if color == "#aaaaaa" -%}
%
{%- endif -%}
{%- if color == "#a28ae5" -%}
~
{%- endif -%}
{%- endmacro -%}

{% persist "annotations" %}
{% set annotations = annotations | filterby("date", "dateafter", lastImportDate) -%}

### Zotero Notes
{% for note in notes -%}
{{note.note}} {%- endfor %}

### Zotero Annotations
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
