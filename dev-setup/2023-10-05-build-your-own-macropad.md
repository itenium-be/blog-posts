---
layout: post
author: Wouter Van Schandevijl
title: "Build your own MacroPad"
subTitle: "Michiel's latest hands-on workshop"
date: 2023-10-05
desc: >
  We created our own MacroPad with 6 customizable
  buttons and a clickable volume knob.
bigimg:
  url: macropad-big.png
  desc: ""
  origin: Midjourney
  prompt: ""
img:
  url: macropad.jpg
  desc: "The End Goal"
  origin: Michiel
  title: ""
categories: dev-setup
tags: [fun,tech-talk]
extras:
  - githubproject: https://github.com/itenium-be/BuildYourOwnMacroPad
    githubtext: "Hands-On Session: build your own MacroPad"
  - url: https://itenium.be/BuildYourOwnMacroPad/
    desc: "Step by step instructions on how to do this yourself!"
toc:
  title: Your MacroPad
  icon: keyboard
---


Last Thursday we built our own MacroPad at itenium.
During this session the focus was on learning to solder
and putting everything together, all under the supervising eye
of Michiel.

<!--more-->


# Friendly Warning

Before we begin soldering...

{% include post/image.html file="macropad-soldering-meme.png" alt="" title="Too bad this info came AFTER the session 😃" desc="Thanks to Mike for the heads up!" maxWidth="360px" %}



# The Components

A [microcontroller](https://www.tinytronics.nl/shop/en/development-boards/microcontroller-boards/others/seeed-studio-seeeduino-xiao-rp2040-dual-core-cortex-m0-separate-headers), [1 rotary encoder](https://www.tinytronics.nl/shop/en/switches/manual-switches/rotary-encoders/rotary-encoder-module) and [6 keyboard switches](https://www.alternate.nl/Sharkoon/Gateron-PRO-2-0-BROWN-Switch-Set-keyboard-switches/html/product/1892291).

{% include post/image.html file="macropad-circuit.png" alt="" title="" desc="" maxWidth="360px" %}


## The Assembly


{% include post/image.html file="macropad-soldering.png" alt="" title="" desc="Soldering the pins on the microcontroller" maxWidth="360px" %}


{% include post/image.html file="macropad-button.png" alt="" title="" desc="Soldering the jumper wires on the keyboard switches" maxWidth="360px" %}


{% include post/image.html file="macropad-wire-mess.png" alt="" title="" desc="Trying to get it all in its casing 😉" maxWidth="360px" %}




# The Session

{% include post/image.html file="macropad-intro.png" alt="" title="" desc="Thank you Michiel for a very cool and fun evening!" maxWidth="500px" %}

{% include post/image.html file="macropad-printing.png" alt="" title="" desc="Printing some additional button caps" maxWidth="360px" %}

{% include post/image.html file="macropad-assembly.png" alt="" title="" desc="Everyone busy at work 😃" maxWidth="360px" %}



# Final Touches

Making them buttons actually do something using [Autohotkey](https://www.autohotkey.com/) or [Microsoft PowerToys](https://learn.microsoft.com/en-us/windows/powertoys/).



{% include post/image.html file="macropad-assembly-finished.png" alt="" title="" desc="Et voila, a stylish MacroPad!" maxWidth="360px" %}


## What's Next

To make our MacroPads truly our own, we definitely have to do a followup Autohotkey session 😎
