---
layout: page
title: About
description: About me
keywords: eeux
comments: true
menu: About
permalink: /about/
---

:tada:
eeux, short for "Liang Xiaojian", a student of software engineer.
Coding is interesting and I record some trivial notes, hoping to help others.


## Contact

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
