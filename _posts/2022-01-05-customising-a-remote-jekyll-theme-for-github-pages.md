---
title: "Customising a remote jekyll theme for Github Pages"
last_modified_at: 2022-01-05T16:20:02-05:00
categories:
  - blog
tags:
  - jekyll
  - blog
  - github pages
---

Using a remote theme adds simplicity, but you can't customise it, right? Not quite. 

## The Motivation

When making this website with Jekyll in a [previous blog post](/blog/creating-this-blog-with-jekyll-and-github-pages/), I opted to use the [remote theme method](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#remote-theme-method).

> Remote themes are similar to Gem-based themes, but do not require Gemfile changes or whitelisting making them ideal for sites hosted with GitHub Pages.
{: .notice--info}
This reduces complexity by abstracting entire folders away (`assets`, `_layouts`, `_includes`, and `_sass`). As always, keep it simple.
{: .notice--info}

Yet although we do not want to do major modifications, if we need to customise the theme, how do we do it? 

## The Problem

Specifically, in the footer all websites in the theme. A sentence giving the framework and a permanent link to its creators website.

![default footer](/assets/images/customising-blog-theme/default-theme-footer.png)

I chose to give credit for the theme, on the [about](/about/) page and in the [README](https://github.com/simon-mcmahon/simon-mcmahon.github.io) of the sites' repository. But felt every page to be a little much.

## The Solution

As always, a [stack overflow question](https://stackoverflow.com/questions/49116592/customize-jekyll-remote-theme-for-github-pages) comes to the rescue. 

Turns out, if you know where the theme file is that needs to be changed and modify it, the local copy overrides the remote theme.
{: .notice--info}

So lets find the correct file! First, download the zip archive of the full theme version from github [here](https://github.com/mmistakes/minimal-mistakes/releases/tag/4.24.0). Extract this file.

Next, search the text of the files for the text `Powered by` to match the text in the footer. I used the file explorer on `Linux Mint 20.3` for this.

![search for Powered by](/assets/images/customising-blog-theme/search-files.png)

Open each result and read the file to see if its context matches. I discovered the relevant file was `_includes/footer.html` with the code:

```html
{%raw%}
<div class="page__footer-copyright">&copy; 
    {{ site.time | date: '%Y' }} {{ site.name | default: site.title }}. {{ site.data.ui-text[site.locale].powered_by | default: "Powered by" }} 
    <a href="https://jekyllrb.com" rel="nofollow">Jekyll</a> &amp; 
    <a href="https://mademistakes.com/work/minimal-mistakes-jekyll-theme/" rel="nofollow">Minimal Mistakes</a>.
</div>
{%endraw%}
```

Finally, in your blog repository. Create the `_includes` folder, and copy the file `footer.html` into it.
Then, modify the HTML to remove the final sentence.

```html
{%raw%}
<div class="page__footer-copyright">&copy; 
    {{ site.time | date: '%Y' }} {{ site.name | default: site.title }}. 
</div>
{%endraw%}
```
Now the footer will be displayed as the simpler...

![modified footer](/assets/images/customising-blog-theme/modified-footer.png)



