---
layout: post
title:  "MathJax for good-looking math formulas in Markdown documents"
date:   2017-02-06 15:42:00
categories: math documentation
---

> One of the main reasons I like Markdown so much is the good-looking displaying feature of source-code and scripts in parallel to descriptive text in one document. The second thing I've always wanted is the possibility to write and then display math expressions in Markdown documents as some of you might know from writing in LaTeX.

`MathJax` is a display engine for mathematical expressions programmed in Javascript. As you might know from writing documentations in LaTeX you may easily write math expressions in the same syntax and display them in Markdown documents.

Edit your `_config.yml` of your jekyll documentation:

```
markdown: kramdown
highlighter: rouge
mathjax: true
```

Open up `_layouts/post.html` and paste in the following `<script>`-body at the top:

```
<script type="text/javascript"
    src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

Create a post and write a math expression in the `amsmath` syntax known from LaTeX:

```
\\[ f_m = \sum_{k=0}^{2n-1} x_k e^{-\frac{2\pi i}{2n} mk}, m = 0, ..., 2n - 1 \\]
```

This will render to:

\\[ f_m = \sum_{k=0}^{2n-1} x_k e^{-\frac{2\pi i}{2n} mk}, m = 0, ..., 2n - 1 \\]

This is beautiful! Isn't it? Everything in one document.

## References

* [MathJax with Jekyll](http://gastonsanchez.com/visually-enforced/opinion/2014/02/16/Mathjax-with-jekyll/)
