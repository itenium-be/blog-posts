---
layout: post
author: Wouter Van Schandevijl
title:  "Create a new blog post"
date:   2019-02-25 00:00:00
desc: >
  How to create a new blog post on this site :)
  Very meta.
img: meta-create-blog-post.png
imgdesc: Photo by Tom Sodoge
imgurl: https://unsplash.com/photos/Qeqg7F9pfuU
interesting:
  - desc: "Font Awesome (for toc.icon)"
    url: https://fontawesome.com/icons
  - desc: "Font mfizz (for toc.icon)"
    url: http://fizzed.com/oss/font-mfizz
  - desc: "Unsplash: Free Images & Pictures"
    url: https://unsplash.com/
  - desc: "35 free stock photography websites"
    url: https://pixelsink.com/35-free-stock-photography-websites/
categories: productivity
tags: [meta]
toc:
  title: Meta
  icon: tag
---

This is something for me (or anyone else really)
because I keep forgetting all this stuff.

**Diving right in**:  
{: .hide-from-excerpt}

Use `{: .hide-from-excerpt}` to do just that.
{: .hide-from-excerpt}

<!--more-->

# New blog post

Create a new file in a folder in `_posts`:  
- `_drafts`: Not published.
- `productivity`: Most things end up here apparently.
- `dotnet`: Everything c#, .NET Core, Visual Studio, etc.
    - `nuget`: .NET libraries and frameworks
- `javascript`: Everything EcmaScript, TypeScript, Node, etc.
    - `npm`: On specific npm packages



# Front Matter

## Template

`img` size must be 360x300.

```yaml
---
layout: post
author: Wouter Van Schandevijl
title:  ""
date:   2019-04-01 00:00:00
desc: >
  
img: 
imgdesc: 
imgurl: 
imgtitle:
categories: productivity | dotnet | javascript
tags: [powershell,autohotkey,sql,angular,testing,excel,git,cheat-sheet,tutorial,windows,product,war-story,regex,debugging,meta]
series: series-unique-name
extras:
  - githubproject: https://github.com/itenium-be/
    githubtext: "Stuff that came into being during the making of this post"
  - url: 
    desc: ""
  - git: 
    desc: ""
interesting:
  - url: 
    desc: ""
toc:
  title: 
  icon: icon-javascript | dot-circle-o (net) | icon-shell (ps1) | eye-slash (c#) | icon-angular | windows | icon-git | car (ahk) | icon-mssql | file-excel-o | icon-mongodb
---

{: .hide-from-excerpt}

<!--more-->

{: .notice--info}

{% raw %}
{% include github-stars.html url="" desc="" %}
{% include kbd k="" l="" %}
{% include post/image.html file="" alt="" title="" desc="" maxWidth="360px" %}
{% include post/youtube.html id="youtubeId" desc="" %}
{% endraw %}

![alt](/assets/blog-images/ "title"){: .img-responsive}

[description][ref]
[ref]: url
```

Some additional, optional front-matter:  
```yaml
---
lastUpdate: 2019-04-01 00:00:00
updates:
  - date: 2019-04-01 00:00:00
    desc: ""
todo:
  - reason: Followup|
    url: 
    desc: 
package-versions:
  moment: 5.2
---

Add API links:  
{% raw %}[String.prototype.match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)
{: style="float: right"}
{: .title-url}
{% endraw %}
```

## Values

- `date`: Make sure the filename and this date match.
- `desc`: Multiline.
    - Displayed on the main page and in the post details aside.
    - In other places the excerpt is displayed. Which is everything before `<!--more-->`.
- `img`:
    - 360x300 pixels
    - Located in `/assets/blog-images/`
    - Optional `imgdesc`
    - `imgurl`: Not in use.
- `categories`:
    - Only three categories: `productivity`, `dotnet` and `javascript`.
    - A new one might be for ex: `ux`, `devops`, `architecture` or `agility`.
    - `TypeScript` or `React` could be tags but not a new category.
- `tags`: A new tag may be more easily added.
    - Configure icon for tags/categories in `_date/blog.yml`.
- `series`: Unique name for the series
- `extras`: Relevant created stuff
- `interesting`: Stuff from elsewhere
- `toc`.`icon`:
    - Do not prefix with fa to use [font-awewome](https://fontawesome.com/icons)
    - Any from [font-mfizz](http://fizzed.com/oss/font-mfizz)
    - Site has also loaded a set from [FlatIcon](https://itenium.be/vendor/flat-icon/flaticon.html)
- `updates`: Add only substantial updates
    - The last x updates will be displayed on the blog
    - `lastUpdate`: Because Liquid

**Legacy**  

- `bigimg`: Never used. Partially implemented?
- `url-short`: Retired.




# Markdown

Can use Html and Liquid includes.

Add inline style with `{: style="margin-left: 20px"}`.
{: style="margin-left: 20px"}


## Liquid Includes

**Show Shortcut**:

{% include kbd k="Control+C,Control+V" l="Description" %}
```
{% raw %}{% include kbd k="Control+C,Control+V" l="Description" %}
{% include kbd k="" l="" %}{% endraw %}
```


**Github Stars**: 

{% include github-stars.html url="Laoujin/dotfiles" desc="Windows dotfiles" %}

```
{% raw %}{% include github-stars.html url="Laoujin/dotfiles" %}
{% include github-stars.html url="" desc="" %}{% endraw %}
```

**Youtube**:

```
{% raw %}{% include post/youtube.html id="youtubeId" desc="text below clip" %}
{% include post/youtube.html id="" desc="" %}{% endraw %}
```

**Images**:  
TODO: Should probably make this clickable to open gallery and/or show full screen?

```
{% raw %}{% include post/image.html file="" alt="" title="" desc="" maxWidth="360px" %}{% endraw %}
```


## Markdown

Create a `<hr>`:  
```
* * *
```

**Links**:  
```
<!-- Inline -->
[description](url)

<!-- Reference -->
[description][ref]
[ref]: url

<!-- Anchor -->
[create an anchor](#anchors-in-markdown)
```


**Images**:  

Bootstrap 3: `.img-responsive`, `.img-rounded`, `.img-thumbnail`, `.img-circle`.

```
<!-- Inline -->
![alt text](/assets/blog-images/screenshot.jpg "Title Text"){: .img-responsive}
![](/assets/blog-images/ ""){: .img-responsive}

<!-- Reference -->
![alt text][ref]

[ref]: /assets/blog-images/ "Title Text"
```

**Quote**:  

> Start with a `>` for a quote.

```md
> This is a quote
```


**Emphasis**:  

*italics*
**bold**
~~strikethrough~~

```
*italics*
**bold**
~~strikethrough~~
```





## Tables

Can use emphasis and inline code blocks inside tabel cells.

**table-code**

```md
| Header1    | Header2                                                 | Remarks |
|------------|---------------------------------------------------------|---------|
|            | 
|            | 
|            | 
|            | 
{: .table-code}
```


| Header1    | Header2                                                 | Remarks |
|------------|:-------------------------------------------------------:|--------:|
| Left       | Center                                                  | Right   |
|            | 
|            | 
|            | 
{: .table-code}



**table-excel**

```
|   | A       | B        | C      | D          | E        | F         | G         | H       |
|---|---------|----------|--------|------------|----------|-----------|-----------|---------|
| 1 | Header  |          |        |            |          |           |           |         |
| 2 |         |          |        |            |          |           |           |         |
| 3 |         |          |        |            |          |           |           |         |
| 4 |         |          |        |            |          |           |           |         |
| 5 |         |          |        |            |          |           |           |         |
{: .table-excel}
```


|   | A       | B        | C      | D          | E        | F         | G         | H       |
|---|---------|----------|--------|------------|----------|-----------|-----------|---------|
| 1 | Header  |          |        |            |          |           |           |         |
| 2 |         |          |        |            |          |           |           |         |
| 3 |         |          |        |            |          |           |           |         |
| 4 |         |          |        |            |          |           |           |         |
| 5 |         |          |        |            |          |           |           |         |
{: .table-excel}



## Notices

This is a `{: .notice}`. Use `**` for **titles**.
{: .notice}

This is a `{: .notice--info}`
{: .notice--info}

This is a `{: .notice--danger}`
{: .notice--danger}



## Escaping

Especially fun when blogging for example about Jekyll itself.
<!-- But meta posts like this are especially fun -->
<!-- On escaping escape sequences for much win!! -->
<!-- https://stackoverflow.com/questions/3426182/how-to-escape-liquid-template-tags -->

**raw / endraw**:  

<!-- Source copyable -->
{% raw %}{% endraw %}

```
{{ '{% raw ' }}%}{% raw %}
no escaping needed
not even }} {{ or %} {%
{% endraw %}{{ '{% endraw ' }}%}
```

**More escaping**:  


- `\|` for not starting a new column in a table
- `\*` for not starting bold
- ``` ``$` `` ```: Use double (or triple, ...) \` when displaying \` inside an inline code block. May not start/end with such backtick.
- `{` doesn't have to be escaped
- `{{ "{{ '{{' " }}}}` for displaying a literal `{{ '{{' }}` (or `&#123;` when not in a code block)
- `{{ "{{ '' " }}}}}}` for displaying a literal `{{ '' }}}}` (or `&#125;` when not in a code block))
- And combined: `{{ "{{ '{{ var ' "}}}}}}` for `{{ '{{ var ' }}}}`
- `{{ "{{ '{%' " }}}}` for displaying a literal `{{ '{%' }}`
- `{{ "{{ '{% var ' "}}}}%}` for displaying a literal `{{ '{% var ' }}%}`
- Assign `{{ "{% assign var = '{{ sth "}}}}' %}` and display  `{{ '{{ var ' }}}}` => `{{ '{{ sth ' }}}}`

<!--
{% assign var = '{{ sth }}' %}
{{ var }}
-->


# Plugins

[Github pages supported plugins](https://pages.github.com/versions)

Some new shiny things to try out in future blog posts:

## jemoji

{% include github-stars.html url="jekyll/jemoji" desc="I give this plugin two :+1:!" %}


Copy ✂️ Paste 📋

╯‵Д′)╯彡┻━┻   angry guy flipping a table

- [Full emoji list](https://unicode.org/emoji/charts/full-emoji-list.html)
- [Smaller list](https://gist.github.com/endolith/157796#file-unicode-official-emojis-txt)

Find on CLI with
{% include github-stars.html url="sindresorhus/emoj" desc="Doesn't work on Windows though" %}
```
npm install --global emoj
emoj [text]
```


## jekyll-gist

{% include github-stars.html url="jekyll/jekyll-gist" desc="" %}

<!-- Boxstarter ID: 12f5d2f76d51ee6c0a49 -->
<!-- https://gist.github.com/Laoujin/12f5d2f76d51ee6c0a49 -->
{% gist 9b0b360b0c0cb39143990e92fa3adcef %}


```
{% raw %}{% gist 9b0b360b0c0cb39143990e92fa3adcef %}{% endraw %}
```


## jekyll-avatar

{% include github-stars.html url="benbalter/jekyll-avatar" %}
{% avatar fabpot %}
```
{% raw %}{% avatar fabpot %}{% endraw %}
```
