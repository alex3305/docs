# Material for Mkdocs landing page

A very simple example on how you can setup your own Mkdocs Material Hero landing page.

## Alternative home page

For this to work we'll need to the following directory structure:

```tree
.
├── .overrides
│   └── home.html
├── docs
│   └── index.md
└── mkdocs.yml
```

### `mkdocs.yml`

You can leave this stock, but for this to work properly the [`navigation.instant` feature](https://squidfunk.github.io/mkdocs-material/setup/setting-up-navigation/) needs to be disabled.

### `docs/index.md`

This file needs to have the following content:

```markdown
---
title: Home
template: home.html
hide:
    - navigation
    - toc
---

```

!!! warning "End with empty line"
    An empty line on the end of the file is required for this to work!

### `.overrides/home.html`

This is our referenced `home.html` override that contains the custom html and styling, like so:

```html
{% extends "base.html" %}

{% block styles %}
{{ super() }}
<style>
  /** Custom CSS goes here **/
</style>
{% endblock %}

{% block content %}
<!-- You can have custom HTML content here -->
{% endblock %}
```

## References:

* [GitHub - squidfunk/mkdocs-material#1996](https://github.com/squidfunk/mkdocs-material/issues/1996#issuecomment-718247120)
* [GitHub - up42/up42-py - docs/theme_override_home](https://github.com/up42/up42-py/tree/master/docs/theme_override_home)
* [Mkdocs - Customizing Your Theme](https://www.mkdocs.org/user-guide/customizing-your-theme/)
