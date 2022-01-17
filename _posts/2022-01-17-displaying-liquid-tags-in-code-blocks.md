---
title: "Displaying Jekyll liquid in code blocks."
last_modified_at: 2022-01-17T16:20:02-05:00
categories:
  - blog
tags:
  - jekyll
  - github pages
  - markdown
  - code
---

In a [recent blog post](/blog/customising-a-remote-jekyll-theme-for-github-pages/), I wrote about modifying Jekyll templates and encountered a problem.

When inside a code block, liquid template elements are still parsed. For example, the markdown
````markdown
```html
{%raw%}<div> 
    {{ site.time | date: '%Y-%m-%d' }}
</div>{%endraw%}
```
````


is displayed instead as ...

```html
<div> 
    {{ site.time | date: '%Y-%m-%d' }}
</div>
```

To override this behavious, use the `{% raw %}{%{% endraw %}raw{% raw %}%}{% endraw %}` tag to begin and `{% raw %}{%{% endraw %}endraw{% raw %}%}{% endraw %}` to end.

And so the correct markdown becomes...

````markdown
```html
{%raw%}{%{%endraw%}raw{%raw%}%}{%endraw%}
{%raw%}<div> 
    {{ site.time | date: '%Y-%m-%d' }}
</div>{%endraw%}
{%raw%}{%{%endraw%}endraw{%raw%}%}{%endraw%}
```
````

Just for fun, here is the even more complex markdown used to correctly format the last code block.

![complex markdown](/assets/images/displaying-liquid-tags/complex-markdown.png)



