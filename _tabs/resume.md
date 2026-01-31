---
title: Resume
permalink: /resume
layout: page
icon: fas fa-file-pdf
order: 6
---

<!-- <div id=dwd style="text-align: right; margin-bottom: 1rem;">
  <a href="{{ '/assets/files/Dhwanit Pandya_Resume_Blog.pdf' | relative_url }}" download="Dhwanit Pandya_Resume_Blog.pdf" 
     style="padding: 6px 12px; color: white; border-radius: 4px; text-decoration: none;font-size: 14px; border: 1px solid;"
     >
    <i class="fas fa-download" style="margin-right: 6px;"></i>Download
  </a>
</div>

<br/>
<div data-no-lightbox style="text-align: center;">
  <img src="/assets/img/Dhwanit Pandya_Resume_Blog.jpg"
       alt="Resume"
       style="width: 900px; height: auto; border-radius: 6px; box-shadow: 0 0 8px rgba(0,0,0,0.3);" />
</div> -->

{% assign doc_id = "1sARpwTC8--F941H6y732gCU8Hjm6Wn1n" %}
{% assign preview_url = "https://docs.google.com/document/d/" | append: doc_id | append: "/preview" %}
{% assign download_url = "https://docs.google.com/document/d/" | append: doc_id | append: "/export?format=pdf" %}

<div id=dwd style="text-align: right; margin-bottom: 1rem;">
  <a href="{{ download_url }}"
     target="_blank"
     style="padding: 6px 12px; color: white; border-radius: 4px; text-decoration: none;font-size: 14px; border: 1px solid;"
     >
    <i class="fas fa-download" style="margin-right: 6px;"></i>Download
  </a>
</div>

<div data-no-lightbox style="text-align: center;">
  <iframe
    src="{{ preview_url }}"
    width="100%"
    height="1100"
    style="border-radius: 6px; border:none;overflow:hidden; box-shadow: 0 0 8px rgba(0,0,0,0.3);">
  </iframe>
</div>

