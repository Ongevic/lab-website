---
layout: page
title: People
permalink: /people/
---

<div class="prose-block">
  <p>This template uses the <code>_people</code> collection for member profiles. Replace the sample records with your own team.</p>
</div>

<div class="prose-block">
  <h2>Lead</h2>
  <div class="people-directory">
    {% assign leads = site.people | where: "group", "Lead" | sort: "sort_order" %}
    {% for person in leads %}
    <article class="directory-card">
      <h3 class="directory-name"><a href="{{ person.url | relative_url }}">{{ person.name }}</a></h3>
      <p class="directory-role">{{ person.position }}</p>
    </article>
    {% endfor %}
  </div>
</div>

<div class="prose-block">
  <h2>Researchers</h2>
  <div class="people-directory">
    {% assign researchers = site.people | where: "group", "Researcher" | sort: "sort_order" %}
    {% for person in researchers %}
    <article class="directory-card">
      <h3 class="directory-name"><a href="{{ person.url | relative_url }}">{{ person.name }}</a></h3>
      <p class="directory-role">{{ person.position }}</p>
    </article>
    {% endfor %}
  </div>
</div>

<div class="prose-block">
  <h2>Students</h2>
  <div class="people-directory">
    {% assign students = site.people | where: "group", "Student" | sort: "sort_order" %}
    {% for person in students %}
    <article class="directory-card">
      <h3 class="directory-name"><a href="{{ person.url | relative_url }}">{{ person.name }}</a></h3>
      <p class="directory-role">{{ person.position }}</p>
      {% if person.phd_program %}
      <p class="directory-meta">{{ person.phd_program }}</p>
      {% endif %}
    </article>
    {% endfor %}
  </div>
</div>

<div class="prose-block">
  <h2>Former Members</h2>
  <ul class="plain-list former-list">
    {% for person in site.lab.people.former_members %}
    <li>
      <span>{{ person.name }}</span>
      {% if person.orcid and person.orcid != "" %}
      <a href="{{ person.orcid }}" target="_blank" rel="noreferrer" class="former-link">ORCID</a>
      {% endif %}
    </li>
    {% endfor %}
  </ul>
</div>
