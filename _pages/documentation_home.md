---
layout: splash
permalink: /documentation
read_time: false
header:
  overlay_color: "#5e616c"
  overlay_image: /assets/images/mm-home-page-feature.jpg
  # actions:
  #   - label: "<i class='fas fa-download'></i> Install now"
  #     url: "/docs/quick-start-guide/"
excerpt: <h2><FONT color="#ffffff">Documentation</FONT></h2>

feature_rowl1:
  - image_path: /assets/images/planet.jpg
    alt: "Model Toolkit"
    title: "Model Toolkit"
    excerpt: >
      (almost) all you need to know to run simulations with IPSL-CM5A2, LMDz, PISCES and ORCHIDEE models, especially for paleo-conditions and boundary conditions design
    url: "/documentation/model-toolkit"
    btn_label: "Read More"
    btn_class: "btn--primary"

feature_rowr1:
  - image_path: /assets/images/coding-on-laptop.jpg
    alt: "Processing Toolkit"
    title: "Processing Toolkit"
    excerpt: >
      Some useful tips to setup your models and transform your netcdf outputs, visualize, display and share your results
    url: "/documentation/processing-toolkit"
    btn_label: "Read More"
    btn_class: "btn--primary"

feature_rowl2:
  - image_path: /assets/images/flow-chart-presentation.jpg
    alt: "Website Toolkit"
    title: "Website Toolkit"
    excerpt: >
      How to manage your website profile and add posts
    url: "/documentation/website-toolkit"
    btn_label: "Read More"
    btn_class: "btn--primary"

---

{% include feature_row id="feature_rowl1" type="left" %}
{% include feature_row id="feature_rowr1" type="right" %}
{% include feature_row id="feature_rowl2" type="left" %}
