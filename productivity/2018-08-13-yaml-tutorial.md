---
layout: post
author: Wouter Van Schandevijl
title:  Yaml tutorial
date:   2018-08-13 20:00:00 +0200
categories: productivity
desc: >
    If there was no such thing as JavaScript,
    Yaml could've been the Xml killer.
img: yaml.png
url-short: https://bit.ly/2BaGoF9
tags: [tutorial]
toc:
    title: Yaml Tutorial
    icon:
---

Everyone happy when the downfall Xml was a fact
with the widespread use of Json. Less tags, more data.
What's there not to like. It's also just so much easier for a human to read.

If it wasn't so straightforward to parse JSON in the omnipresent JavaScript,
Yaml might have had a good chance to take over the world being **the** most readable
data serialization language.

<!--more-->

# YAML Ain't Markup Language

Before we dive into the "code":

- Extensions: `.yaml`, `.yml`
- Indenting with spaces. (no tabs!)
    - Correct indentation is mandatory!
    - 2 spaces is favored (because it makes arrays align?)
    - For arrays, the `-` counts as indentation
- Keys may contain spaces, and pretty much anything when enclosed within single quotes


When unsure what something does exactly, try it online: [json <-> yaml][json2yaml]!

## Scalars

```yml
# This is a comment
key: value
int: 7
float: 0.5
bool: true
null: null
```

This would be equal to the json: `{key: 'value', int: 7, float: 0.5, bool: true, null: null}`

<br>
**Dates**:  
```yaml
dt: 2018-12-18T02:59:43.1Z
dt_alt: 2017-12-17 16:59:43.10 -5
date: 2016-12-16
```

Json: `{
    dt: '2018-12-18T02:59:43.100Z',
    dt_alt: '2017-12-17T21:59:43.100Z',
    date: '2016-12-16T00:00:00.000Z'
}`


<br>
**Casting**:  
- `key: !!str 0.5` => "0.5"
- `key: !!float 0` => 0.0


## Strings

```yml
# To be on the safe side, quote all strings
# that contain any of the following []{}:>|
str1: This is a string
str2: 'has ''one'' escape'
str3: "has many \", \t\r\n\0"

# With preserve extra whitespace starts the current level of indentation.
preserve: |
  multi-line
      with newlines and
    additional whitespace
          preserved

fold: >
  These lines
  will be folded
  into a single line

  Blank lines denote
  paragraph breaks
```



## Mappings and Sequences

```yaml
map:
  name: Billy
  age: 16

map_alt: {name: Billy The Kid, age: 16}

array:
  # To nest arrays, just increase the indenting
  - 9
  - true
  - a string

array_alt: [9, true, a string]

array_map:
  - id: 5
    name: Bart
    hobbies:
      - Skateboarding
      - Mischievousness
  - id: 6
    name: Maggie
    traits:
      lang: null
      proxy: Marge
```

The Json for `array_map`:  
```json
[{
    id: 5,
    name: 'Bart',
    hobbies: ['Skateboarding', 'Mischievousness']
}, {
    id: 6,
    name: 'Maggie',
    traits: {lang: null, proxy: 'Marge'}
}]
```


## Anchors 

**Using anchors**:  
```
billing: &address1
  street: Brook Street 15
  town: Davenport

shipping: *address1
```

After this, the content of `shipping` is the same as that of `billing`


**Merge mappings**  
```yml
male: &man
  gender: M
  age: null

female: &woman
  gender: F
  age: null

Bob:
  spouse: Alice
  <<: *man
  age: 32

Catherine:
  <<: *woman
  spouse: Mary
```

Json:  
- Bob: `{spouse: 'Alice', gender: 'M', age: 32}`  
- Catherine: `{gender: 'F', age: null, spouse: 'Mary'}`  


## Other

**Binary**:  
```
gif_file: !!binary |
  R0lGODdhDQAIAIAAAAAAANn
  Z2SwAAAAADQAIAAACF4SDGQ
  ar3xxbJ9p0qa7R0YxwzaFME
  1IAADs=
```

**Sets**:  
```yml
set:
  ? item1
  ? item2
  ? item3
```

Json: `{set: {item2: null, item3: null, item1: null}}`



# Implementations

I first came into contact with Yaml when using Jekyll, where it is used quite extensively.
Aside from Jekyll, it is used for a many things, in Spring, Travis CI, Grav, in the cloud, ...
and [parsers are available for all languages][yaml-org].

It's not all [moonshine and roses][yaml-sucks] though as the implementations
tend to yield different results for more exotic inputs (and sometimes also for not so exotic input).



**YamlDotNet**  
{% include github-stars.html url="aaubry/YamlDotNet" desc="For .NET, there is YamlDotNet" %}

```c#
Install-Package YamlDotNet

using YamlDotNet.Serialization;
var deserializer = new DeserializerBuilder().Build();
using (var file = File.OpenText("_config.yml"))
{
    return deserializer.Deserialize<T>(file);
}
```

**js-yaml**  
{% include github-stars.html url="nodeca/js-yaml" desc="For Node, there is js-yaml" %}

```js
npm install js-yaml

yaml = require('js-yaml');
fs = require('fs');
try {
  var doc = yaml.safeLoad(fs.readFileSync('_config.yml', 'utf8'));
} catch (e) {
}
```

[yaml-org]: http://yaml.org
[yaml-sucks]: https://github.com/cblp/yaml-sucks
[json2yaml]: https://www.json2yaml.com/
