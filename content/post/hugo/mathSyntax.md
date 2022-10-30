---
title: "如何在Hugo中使用markdown的数学公式"
date: 2022-10-29T22:16:55+08:00
draft: false
math: true

tags: ["Hugo", "math syntax", "数学公式"]
categories: ""
description: ""
---

渲染数学公式有两种方式，一种是 `mathjax` ，另一种是 `KaTex`，因为 `KaTex` 比较快，所以使用这种方式。

具体步骤如下：

1. 首先创建一个部分的模板文件，里面链上 `CSS` 文件和 `JS` 文件，这些脚本会自动将公式渲染。
`/layouts/partials/helpers/katex.html`。

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.3/dist/katex.min.css">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.3/dist/katex.min.js"></script>

<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.3/dist/contrib/auto-render.min.js" onload="renderMathInElement(document.body);"></script>
```

不要只顾着复制，看一下版本更新，我写博客的时候已经更新到 `0.16.3` 了，把版本号替换一下就可以了。

具体更新看这里 -> [update](https://katex.org/docs/browser.html)。

2. 在头文件中引用刚才的模板。
`/layouts/partials/head.html`

```template
{{ if .Params.math }}{{ partial "helpers/katex.html" . }}{{ end }}
```

3. 在需要渲染数学公式的博客的 `frontMatter` 中使用 `math` 参数。

```
---
math: true
---
```

4. 如果要使用内联公式样式，还需要添加以下代码：
`/layouts/partials/helpers/katex.html`

```html
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
            delimiters: [
                {left: "$$", right: "$$", display: true},
                {left: "$", right: "$", display: false}
            ]
        });
    });
</script>
```

如果你要使用内联样式，只需要用一个 `$` 符号包裹，例如`$x = {-b \pm \sqrt{b^2-4ac} \over 2a}$` 的效果就是这样的：$x = {-b \pm \sqrt{b^2-4ac} \over 2a}$。

如果要独占一行居中就要使用两个 `$$`，而且不能和公式在同一行。

```
$$
x = {-b \pm \sqrt{b^2-4ac} \over 2a}
$$
```

效果如下：

$$
x = {-b \pm \sqrt{b^2-4ac} \over 2a}
$$

> ps: 亲测，加不加都可以使用内联样式

> reference: https://mertbakir.gitlab.io/hugo/math-typesetting-in-hugo/