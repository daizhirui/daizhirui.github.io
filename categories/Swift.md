---
layout: category
title: "Category: Swift"
---

`Swift` is a programming language designed by `Apple Inc.`. It is a type-safe language.

Because of the popularity of iOS, as a language used for iOS App development, `Swift` is getting more and more popular.

Here are my notes about `Swift`.

<ul>
    {% for post in site.categories.Swift %}
    <li>
        <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
    </li>
    {% endfor %}
</ul>
