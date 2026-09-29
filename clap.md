---
layout: single
title: "CLAP"
permalink: /clap/
---

## Upcoming CLAP

Add your name and a few words about what you would like to discuss.

<iframe
  src="URL_DE_TON_PAD"
  width="100%"
  height="650"
  style="border:1px solid #ccc; border-radius:4px;">
</iframe>

---

## Previous meetings

{% assign clap_reports = site.pages
   | where_exp: "page", "page.path contains 'clap/CLAP'"
   | sort: "clap_number"
   | reverse %}

{% for report in clap_reports %}

<details style="margin-bottom: 1em;">
  <summary style="cursor:pointer; font-weight:bold;">
    {{ report.title }}
  </summary>

  <div style="padding: 1em 0 0 1em;">
    {{ report.content }}
  </div>
</details>

{% endfor %}
