---
layout: default
---
{::options parse_block_html="true" /}

**"Art" by Silas Bartha**

{% for gallery in site.data.art %}

## {{ gallery.title }}
{: style="text-align:center;"}

---

<div style="display: flex; width: 100%; justify-content: center;">
{% for piece in gallery.pieces %}

{% case piece.type %}
{% when "image" %}

| ![{{ piece.title }}]({{ piece.url }}){: class="{% unless piece.pixelated %} smooth {% endunless %}" style="background-image: url('/assets/images/checkerboard.png'); max-width: 250px;"} |
| :-: |
| **{{ piece.title }}** |
| *{{ piece.description }}* |
{: class="bordered"}
{% endcase %}
{% endfor%}
</div>
{% endfor %}
