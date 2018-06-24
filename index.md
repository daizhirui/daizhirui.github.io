---
layout: home
title: Welcome to read my notebook!
date: 2018-06-24
---

<style type="text/css">
    tab1 { margin-left: 4em; }
</style>

# All articles

<ul>
    {% for post in site.posts %}
    <li>
        <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
        <div align="right"><small><strong> - - - - {{ post.date | date: "%B %e, %Y" }}, {{ post.categories }}</strong></small></div>
    </li>
    {% endfor %}
</ul>

# My Projects

## Camel Microelectronic

### CamelStudioX

`CamelStudioX` is an IDE for Camel Microelectronic's products. We can create a project, write C language code, compile the code to an image and then upload it to a chip designed by Camel Microelectronic. What's more, we can use an integrated serial tool to run the program we upload to the chip.

Now, I develop a `macOS` version CamelStudioX in `Swift`. You can visit the [repository](https://github.com/daizhirui/CamelStudioX_Mac) of this software to learn something about macOS Application programming.

You can also download this macOS version [here](https://github.com/daizhirui/CamelStudioX_Mac/releases/latest).

### CamelStudioX Library

This library is designed for M2, a MIPS embedded system. Except some modules designed specially for M2, there are also some useful softwares that can be transplant to other platforms, e.g. DES(Data Encryption Standard).

Click [here](https://github.com/daizhirui/CamelStudio_Library) to visit this library.

Click [here](https://daizhirui.github.io/CamelStudio_Library/) to read the documentation of this library.
