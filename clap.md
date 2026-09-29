---
layout: single
title: "CLAP"
permalink: /clap/
---

<style>

/* =========================================================
   CLAP WHITEBOARD
   ========================================================= */

.clap-board {
  width: 100%;
  height: 380px;
  border: 1px solid #ddd;
  border-radius: 6px;
  margin-top: 1em;
  margin-bottom: 0.7em;
}

.clap-edit-link {
  display: inline-block;
  margin-bottom: 2em;
  padding: 8px 14px;
  border: 1px solid #aaa;
  border-radius: 5px;
  text-decoration: none;
}


/* =========================================================
   MEETING CARDS
   ========================================================= */

.clap-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-bottom: 1em;
  background: #fff;
  overflow: hidden;
}

.clap-card summary {
  cursor: pointer;
  list-style: none;
  padding: 1em 1.2em;
}

.clap-card summary::-webkit-details-marker {
  display: none;
}

.clap-card summary:hover {
  background: #f8f8f8;
}

.clap-card-title {
  font-weight: bold;
  font-size: 1.05em;
  margin-bottom: 0.5em;
}


/* Preview of the beginning of the report */

.clap-preview {
  color: #555;
  font-size: 0.92em;

  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;

  overflow: hidden;
  line-height: 1.5em;
  max-height: 4.5em;
}

.clap-preview::after {
  content: " ...";
}


/* Full report */

.clap-full-report {
  padding: 0 1.2em 1.2em 1.2em;
  border-top: 1px solid #eee;
}


/* When a card is open, hide its preview */

.clap-card[open] .clap-preview {
  display: none;
}


/* =========================================================
   OLDER MEETINGS
   ========================================================= */

.clap-older {
  margin-top: 1em;
}

.clap-older > summary {
  cursor: pointer;
  display: inline-block;
  padding: 8px 14px;
  border: 1px solid #aaa;
  border-radius: 5px;
  font-weight: bold;
  margin-bottom: 1em;
}

.clap-older > summary:hover {
  background: #f5f5f5;
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


<!-- ======================================================
     THREE MOST RECENT MEETINGS
     ====================================================== -->

{% for report in clap_reports limit:3 %}

<details class="clap-card">

  <summary>

    <div class="clap-card-title">
      {{ report.title }}
    </div>

    <div class="clap-preview">
      {{ report.content | strip_html | strip_newlines }}
    </div>

  </summary>

  <div class="clap-full-report">
    {{ report.content }}
  </div>

</details>

{% endfor %}


<!-- ======================================================
     OLDER MEETINGS
     ====================================================== -->

{% if clap_reports.size > 3 %}

<details class="clap-older">

  <summary>
    Show older meetings
  </summary>

  {% for report in clap_reports offset:3 %}

  <details class="clap-card">

    <summary>

      <div class="clap-card-title">
        {{ report.title }}
      </div>

      <div class="clap-preview">
        {{ report.content | strip_html | strip_newlines }}
      </div>

    </summary>

    <div class="clap-full-report">
      {{ report.content }}
    </div>

  </details>

  {% endfor %}

</details>

{% endif %}
