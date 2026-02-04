---
layout: post
author: Wouter Van Schandevijl
title: "Goodbye Cmder"
subTitle: "You have been a loyal friend"
date: 2025-11-24
desc: >
  I have been using cmder for years but its development
  has slowed down so maybe time to look at some alternatives?
bigimg:
  url: -big.png
  origin: Midjourney
  prompt: ""
img:
  url: .png
  desc: ""
  origin: Midjourney
  prompt: ""
categories: dev-setup
tags: [tutorial]
---

Let's look at some options for a Windows terminal in which we'll be running PowerShell
most of the time but bash when we're in a pinch.

Obviously we want the basics like multi tabs, 

What am I missing in Cmder:
- True colory emoji support
- Scripting?
- Opening predefined windows?

Ask ChatGPT what else we would need


https://github.com/cdleon/awesome-terminals?tab=readme-ov-file#windows


https://github.com/saehun/awesome-terminal
https://github.com/alebcay/awesome-shell
https://github.com/k4m4/terminals-are-sexy --> See: Terminal Emulation Applications


<!--more-->

# Contenders

We'll be looking at these contenders:

{% include github-stars.html url="microsoft/terminal" desc="The new Windows Terminal and the original Windows console host" %}
{% include github-stars.html url="eugeny/tabby" desc="A terminal for a more modern age (ex Terminus)" %}
{% include github-stars.html url="alacritty/alacritty" desc="A cross-platform, OpenGL terminal emulator" %}
{% include github-stars.html url="vercel/hyper" desc="A terminal built on web technologies" %}
{% include github-stars.html url="kovidgoyal/kitty" desc="Cross-platform, fast, feature-rich, GPU based terminal" %}
{% include github-stars.html url="cmderdev/cmder" desc="Lovely console emulator package for Windows" %}
{% include github-stars.html url="warpdotdev/warp" desc="Modern terminal with AI features (not free)" %}
{% include github-stars.html url="wezterm/wezterm" desc="GPU-accelerated cross-platform terminal emulator and multiplexer" %}
{% include github-stars.html url="felixse/FluentTerminal" desc="A Terminal Emulator based on UWP and web technologies (inactive since 2024)" %}
{% include github-stars.html url="sedwards2009/extraterm" desc="The swiss army chainsaw of terminal emulators" %}


<!--block1-->


# Quick Comparison

| Repository          | Stars | Commits | Issues | PRs | Forks | Last Commit | Release | Language       |
|---------------------|------:|--------:|-------:|----:|------:|-------------|---------|----------------|
| [MS Terminal][0]    | 101k  | 4.7k    | 1.6k   | 49  | 8.9k  |             |         | C++            |
| [Tabby][1]          | 67.3k | 6.3k    | 2.4k   | 66  | 3.8k  |             |         | TypeScript     |
| [Alacritty][2]      | 61.1k | 2.4k    | 315    | 11  | 3.2k  |             |         | Rust           |
| [Hyper][3]          | 44.5k | 4.0k    | 941    | 77  | 3.6k  |             |         | TypeScript     |
| [Kitty][4]          | 29.9k | 16.8k   | 9      | 8   | 1.2k  |             |         | Python         |
| [Cmder][5]          | 26.7k | 1.7k    | 52     | 9   | 2.1k  |             | Yearly  | PowerShell/C++ |
| [Warp][6]           | 25.3k | 335     | 4k     | 0   | 587   |             |         | Rust           |
| [Wezterm][7]        | 22.6k | 8.5k    | 1.3k   | 145 | 1.1k  |             |         | Rust           |
| [FluentTerminal][8] | 9.5k  | 661     | 250    | 4   | 454   |             |         | C#             |
| [ExtraTerm][9]      | 2.7k  | 4.8k    | 111    | 3   | 127   |             |         | TypeScript     |
{: .table-code}

https://github.com/mbadolato/iTerm2-Color-Schemes
--> This has color schemes for terminals not yet listed here 😀
Terminal, Konsole, PuTTY, Xresources, XRDB, Remmina, Termite, XFCE, Tilda, FreeBSD VT, Terminator, Kitty, MobaXterm, LXTerminal, Microsoft's Windows Terminal, Visual Studio, Alacritty, Ghostty
https://github.com/Swordfish90/cool-retro-term
https://github.com/mjswensen/themer

--> We should look for themes for each terminal separately
https://github.com/ryanoasis/nerd-fonts


TODO: Consider adding: # Contributors
TODO: Warp is not free, just put it below Deprecated

TODO: add https://github.com/ConEmu/ConEmu
TODO: https://github.com/termux/termux-app --> only for Android, but looks like you can also install on windows?
TODO: https://github.com/kingToolbox/WindTerm (9 months ago)
TODO: https://github.com/zellij-org/zellij


**Deprecated:** [Babun](https://github.com/babun/babun), [Console2](https://github.com/cbucher/console)


<!--block1-->

[0]: https://github.com/microsoft/terminal
[1]: https://github.com/eugeny/tabby
[2]: https://github.com/alacritty/alacritty
[3]: https://github.com/vercel/hyper
[4]: https://github.com/kovidgoyal/kitty
[5]: https://github.com/cmderdev/cmder
[6]: https://github.com/warpdotdev/warp
[7]: https://github.com/wezterm/wezterm
[8]: https://github.com/felixse/FluentTerminal
[9]: https://github.com/sedwards2009/extraterm





# Customization

| Repository | Stars | Forks | Commits | Open Issues | Open PRs | Language | Description |
|------------|-------|-------|---------|-------------|----------|----------|-------------|
| [starship/starship](https://github.com/starship/starship) | 52.4k | 2.3k | 4,023 | 765 | 193 | Rust | The minimal, blazing-fast, and infinitely customizable prompt for any shell |
{: .table-code}


PROMPTs:
https://github.com/spaceship-prompt/spaceship-prompt


[Installation Guide](https://starship.rs/guide/#%F0%9F%9A%80-installation)




# Old List



https://github.com/microsoft/terminal  : 101k stars :  4.7k commit : releases every month :  1.6k issues, 51 open PRs : c++
The new Windows Terminal and the original Windows console host, all in the same place!

https://github.com/cmderdev/cmder : 27k stars : 1.8k commits : releases every year : 52 issues : 8 open PRs : PowerShell / C++
Lovely console emulator package for Windows


https://github.com/alacritty/alacritty : 61k stars : 2.4k commits : every month / every 6 months : 325 issues : 12 PRs : Rust
A cross-platform, OpenGL terminal emulator.

https://github.com/warpdotdev/warp : 25k stars : 335 commits : no releases : 4k issues : 0 prs :
Nvm, not free?

https://github.com/eugeny/tabby : 67k : 6.3k commits : every few months : 2.4k issues : 66 prs : TypeScript : last commit 2 weeks ago
A terminal for a more modern age


https://github.com/felixse/FluentTerminal : 9.5k : 661 commits : last in 2022 :  250 issues : 4 prs : 2 years ago : 58 contributors : 454 forks
A Terminal Emulator based on UWP and web technologies.

https://github.com/vercel/hyper : 44k : 4k commits : 941 issues : 77 prs : TypeScript : last year last commit : 269 contributors : 3.6k forks : last release in 2023 : last commit last year
A terminal built on web technologies

https://github.com/kovidgoyal/kitty : 30k : 17k commits : 9 issues : 8 prs : monthly : 30 min ago : 360 contributors : Python/C/Go : 1.2k forks
If you live in the terminal, kitty is made for you! Cross-platform, fast, feature-rich, GPU based.


https://github.com/wezterm/wezterm : 22k stars : 1.3k issues : 15 prs : rust : 8.5k commits : last release 2024 : 380 contributors : 1.1k forks
A GPU-accelerated cross-platform terminal emulator and multiplexer written by @wez and implemented in Rust


https://github.com/sedwards2009/extraterm : 2.7k stars : 4.8k commits :
And after that we need to customize further!

https://github.com/starship/starship : 52k stars
☄🌌️ The minimal, blazing-fast, and infinitely customizable prompt for any shell!
https://starship.rs/guide/#%F0%9F%9A%80-installation













  Starship – Cross-shell prompt, fast, customizable, shows git status, battery, jobs, etc.
  Oh My Posh – Windows-focused prompt theming framework (especially PowerShell).
  Oh My Zsh – Classic Zsh framework for Linux/macOS, works in WSL.: https://github.com/ohmyzsh/ohmyzsh
  Prezto – Lightweight alternative to Oh My Zsh.
  Powerlevel10k – Super fast Zsh theme with tons of info on prompt.

OhMyBash


only linux?
https://github.com/fish-shell/fish-shell


 

 

2. Productivity / Navigation Tools

fzf – Fuzzy finder for files, history, git branches, etc.
  https://github.com/BurntSushi/ripgrep

bat – cat replacement with syntax highlighting and Git integration. - https://github.com/sharkdp/bat
delta – Git diff pager with colors, syntax highlighting, and side-by-side view.
ripgrep (rg) – Ultra-fast search tool, replacement for grep.
exa – Modern ls replacement, better visuals, git info, icons.
fd – Faster, simpler find replacement. -- https://github.com/sharkdp/fd
autojump / zoxide – Jump quickly between directories based on usage.
https://github.com/nicolargo/glances
https://github.com/withfig/autocomplete
https://github.com/nvbn/thefuck
cd: https://github.com/ajeetdsouza/zoxide


--> all in one: https://github.com/ibraheemdev/modern-unix

https://github.com/Textualize/rich


MAN:
https://github.com/tldr-pages/tldr
https://github.com/chubin/cheat.sh

 

. Git / Code Helpers

lazygit – Interactive terminal UI for Git, easy staging/committing/branching. -- https://github.com/jesseduffield/lazygit
gh CLI – GitHub command line tool for PRs, issues, releases, etc.
glab – GitLab CLI, similar to gh.
diff-so-fancy – Makes git diff output more human-readable.
 

 

5. Fun / Miscellaneous

cowsay / lolcat – Fun output for messages / scripts.
htop / btop – Interactive process monitors.
neofetch / screenfetch – Shows system info / ASCII art in terminal.
fig / copilot CLI – AI-assisted autocomplete for terminal commands (if available for Windows by 2025).



