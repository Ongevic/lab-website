---
layout: default
title: Example Lab
---

<section class="centre-hero" markdown="0">
  <div class="container centre-hero-shell">
    <div class="centre-hero-main">
      <p class="centre-kicker">Research Group Template</p>
      <h1 class="centre-title">{{ site.lab.full_name }}</h1>
      <p class="centre-acronym">{{ site.lab.name }}</p>
      <p class="centre-summary">{{ site.lab.tagline }}</p>
      <p class="centre-affiliation">
        {{ site.lab.department }} at {{ site.lab.university }}.
      </p>
    </div>
  </div>
</section>

<section class="centre-section" markdown="0">
  <div class="container prose-block">
    {% for paragraph in site.lab.overview.intro %}
    <p>{{ paragraph }}</p>
    {% endfor %}
    <h2>Main Objectives</h2>
    <ul class="plain-list">
      {% for item in site.lab.overview.objectives %}
      <li>{{ item }}</li>
      {% endfor %}
    </ul>
  </div>
</section>
