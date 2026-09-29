---
layout: single
title: "CLAP"
permalink: /clap/
---

<style>
.clap-board {
  width: 100%;
  height: 700px;
  border: 1px solid #ddd;
  border-radius: 6px;
  margin-top: 1em;
  margin-bottom: 1em;
}

.clap-edit-link {
  display: inline-block;
  margin-bottom: 2em;
  padding: 8px 14px;
  border: 1px solid #aaa;
  border-radius: 5px;
  text-decoration: none;
}

.clap-report {
  margin-bottom: 0.8em;
  border-bottom: 1px solid #eee;
  padding-bottom: 0.5em;
}

.clap-report summary {
  cursor: pointer;
  font-weight: bold;
  font-size: 1.05em;
}

.clap-report-content {
  padding: 1em 0.5em;
}
</style>


## Upcoming CLAP

Add your name and a few words about what you would like to discuss at the next CLAP.

<iframe
  class="clap-board"
  src="https://docs.google.com/document/d/12nmShMUq6tPkvEtAEUTYbfu4RLOCEUAWYOQRbifxS4o/edit?tab=t.0&rm=minimal">
</iframe>

<a
  class="clap-edit-link"
  href="https://docs.google.com/document/d/12nmShMUq6tPkvEtAEUTYbfu4RLOCEUAWYOQRbifxS4o/edit?tab=t.0"
  target="_blank">
  Open CLAP board in Google Docs
</a>

---

## Previous meetings

{% assign clap_reports = site.pages
   | where_exp: "page", "page.path contains 'clap/CLAP'"
   | sort: "clap_number"
   | reverse %}

{% for report in clap_reports %}

<details class="clap-report">

  <summary>
    {{ report.title }}
  </summary>

  <div class="clap-report-content">
    {{ report.content }}
  </div>

</details>

{% endfor %}
