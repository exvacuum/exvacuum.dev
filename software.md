---
layout: default
---
{::options parse_block_html="true" parse_span_html="true" /}

**Software by Silas Bartha**
{% for category in site.data.software %}
## {{ category.category }}
{: style="text-align:center;"}

---
{% for entry in category.entries %}
### {{ entry.title }}: {{ entry.brief }}
{: #{{ entry.title }} style="text-align:center;"}
---
<div class="container-flex">


![]({{ entry.cover_image }}){: 
    class="smooth bordered" 
    style="
        max-width:250px;
        margin-right: 10px;
    "
}

<div class="container-flex" style="flex-direction: column;">

{{ entry.full }}

{% for link in entry.links %}
![]({{ link.icon }}){: .inline-icon}[{{ entry.title }} {{ link.title }}]({{ link.url }}){% if link.external == true %}{: target="_blank" rel="noreferrer"}{% endif %}
{% endfor %}
</div>
</div>
{% endfor %}
{% endfor %}

