---
layout: post
author: Wouter Van Schandevijl
title: "Performance optimization for 'bundle exec jekyll serve'"
subTitle: "Because waiting 20+ seconds is just not acceptable"
date: 2025-10-03
desc: >
  I'm such an apologist that I've put up with that 20 second delay
  for far too long!
bigimg:
  url: jekyll-performance-big.png
  prompt: "Jekyll as a cheetah made of code running at light speed, server racks forming its jungle, websites materializing in its wake, predator of slow load times --ar 700:131"
  origin: Midjourney
img:
  url: jekyll-performance.png
  prompt: "Dr Jekyll (from Mr Hyde) serving websites at light speed, silver tray with holographic sites materializing instantly, Victorian steampunk aesthetic mixed with cyberpunk technology, gears turning at impossible velocities --ar 6:5"
  origin: Midjourney
categories: ruby
tags: [powershell,windows,war-story]
toc:
  title: Jekyll Serve
  icon: icon-ruby
package-versions:
  - package: Jekyll
    version: 4.2.2
socials:
  youtube: ""
  linkedin: ""
  instagram: ""
  twitter: ""
  facebook: ""
---

Every change to a blog post on this website took a whopping 20 second
delay before I could see the result.

The [solution](https://talk.jekyllrb.com/t/exclude-jekyll-feed-in-development-environment/2681/4)
was really simple, really. And was committed in the README as a TODO over 3 years ago.

<!--more-->

# Upgrade Jekyll

Upgrading Jekyll from v3.9.0 to 4.2.2 already brought this
reload times down from **20s to 12s**.

After updating the `Gemfile`, I also had to upgrade a few other gems:

```txt
gem "jekyll", "4.2.2"

group :jekyll_plugins do
  gem "kramdown-parser-gfm"
  gem "jekyll-feed", "~> 0.17"
  gem "jekyll-paginate", "~> 1.1.0"
  gem "jemoji", "~> 0.13.0"
  gem "jekyll-gist", "~> 1.5.0"
  gem "jekyll-avatar", "~> 0.8.0"
  gem "jekyll-redirect-from"
end
```

And then apply the changes with `bundle update`.

There probably isn't much you have to do when [upgrading from v3 to v4](https://jekyllrb.com/docs/upgrading/3-to-4/).

- `ruby -v` needs to be >=2.7.0
- {% raw %}`{{ site.baseurl }}/{% post_url some-post.md %}` -> `{% post_url some-post.md %}`{% endraw %}



# Incremental Serving

The next logical step is to only re-render what has actually changed.
This reduced reload times from **12s to 3s** 🎉🥳


```ps1
bundle exec jekyll serve --incremental

# or:
bundle exec jekyll serve -I
```

## Windows Directory Monitor

{% include github-stars.html url="Maher4Ever/wdm" desc="A threaded directories monitor for Windows." %}

For Windows, you also need to add `wdm` to the `Gemfile`:

```txt
# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.2.0", :platforms => [:mingw, :x64_mingw, :mswin]
```

`bundle install` and you're ready to go!


# Development config.yml

This can be improved even further by not doing some plugin work
that is typically not necessary during development anyway.

```ps1
bundle exec jekyll serve --incremental --config _config_dev.yml
```

In the copied `_config_dev.yml` I removed all the gems in
`group :jekyll_plugins do`.

This however only reduced the reload time by 0.5s which wasn't worth
it for me as I'd have to keep two config.ymls in sync.

## Limit Posts

In the `_config_dev.yml`, you can specify to only render the last x posts.
This turned out to be not very useful because the profiler told me
that most time is spent on reading and not so much on the rendering
phase.

```yaml
# While this seems like a super great idea, this no longer makes
# it possible to use post_url to point to a post that is older..
limit_posts: 20
```


# Profiling

If it's still taking too long, you can use `--profile` to figure out
what's going on.

```ps1
bundle exec jekyll serve --profile
```


# PowerShell Profile

I have these added to my [ps profile]({% post_url dev-setup/powershell/2017-04-11-powershell-profiles %}),
because who wants to remember/write all the bundle exec etc etc 😀

```ps1
function Start-Jekyll {
	bundle exec jekyll serve --incremental
}
function Start-JekyllWithDrafts {
	bundle exec jekyll serve --drafts --incremental
}
function Start-JekyllDev {
	bundle exec jekyll serve --incremental --config _config_dev.yml
}
function Start-JekyllDevWithDrafts {
	bundle exec jekyll serve --drafts --incremental --config _config_dev.yml
}

Set-Alias bejs Start-Jekyll
Set-Alias bejsd Start-JekyllWithDrafts
Set-Alias bejsdev Start-JekyllDev
Set-Alias bejsddev Start-JekyllDevWithDrafts
```
