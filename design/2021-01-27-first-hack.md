---
layout: post
author: Wouter Van Schandevijl
title:  "First Hack"
subTitle: "... If you can call it that much."
date:   2021-01-27 00:00:00
desc: >
  A story of an evil user.
bigimg:
  url: first-hack-big.png
  desc: "Photo by Jay Wennington"
  origin: https://unsplash.com/photos/JrDou1DZ95E
img:
  url: first-hack.png
  desc: "Photo by McGill Library"
  origin: https://unsplash.com/photos/xYRM6tusURI
categories: design
tags: [fun,war-story]
extras:
  - githubproject: https://github.com/Laoujin/first-hack
    githubtext: "First security hole in exploited in a game :)"
toc:
  title: 〠 First Hack
---

A long, long time ago, when games like [Utopia](https://utopia-game.com) and [Planetarion](http://www.planetarion.com/) were all the rage,
I started playing Ambar (since down) with a few friends. This could be the story of why one should have an IT-guy in the team.

<!--more-->

# The Strategy

![Ambar logo](/assets/blog-images/first-hack-ambar-logo.jpg "Ambar logo")

During one age of the browser game Ambar, we were playing an empire in which our strategy was to grow like mad but without training any troops.
If we could outgrow everyone fast enough, no one would be able to open a magical portal to our empire to attack us, since portal-opening was based on empire size.
This was kind of the idea because as you can guess, having no troops in a war game usually is.. problematic.


# The Portal

And... Turns out we hadn't grown enough, fast enough because right after the initial no-attack period an enemy empire did succeed in opening a portal to us 😲.  
Usually this is not really a problem, as there is a spell to close a portal should someone open one to an "undesirable" target - in our case **any** other empire.

Unfortunately, for our "strategy" to work, we all needed to pick a certain race providing us with the correct perks and bonuses during registration.
And of course this race did not posses the capability to cast the "Close Portal" spell. At this point, we were ready for the slaughterhouse.


# The Hack

Casting a spell in Ambar was as simple as selecting it from a dropdown menu:

```html
<select>
    <option value="0">No Spell Selected</option>
    <option value="1">Divine Sight</option>
    <option value="4">Echos of the Past</option>
    <option value="6">Open Portal</option>
    ... and more spells ...
</select>
```

And all I did was save the Html locally, add the spells not available to my race to the `<select>`
and add a little [`<base />`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) tag.

```html
<base href="http://www.darklights.com/cgi-bin/game/login">
<select>
    <option value="7">Close Portal</option>
    ... and all other spells in game ...
</select>
```

And 💥, the portal closed, our empire saved from those heinous invaders and me wondering why they didn't do this little check on the backend.

I did get an IM from someone of the attacking empire asking how exactly we managed to magically close the portal since no one in our empire had the required race to do so ;)


# The Conclusion

All user input is evil.

Have a backup plan in case risky plan A goes awry.

I'm a cheat and should've been banned.
