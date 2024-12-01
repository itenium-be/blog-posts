---
layout: post
author: Wouter Van Schandevijl
title: "Can you reset my mac"
subTitle: "Recover your mac without password"
date: 2024-12-01
desc: >
  Apple products are so easy and straightforward to use,
  that is, until you need to do anything involving the
  Apple Account.
bigimg:
  url: reset-imac-big.png
  origin: Midjourney
  prompt: "a tree with a single apple and a snake in the tree --ar 700:131"
img:
  url: reset-imac.png
  desc: "Rotten Apple ;)"
  origin: Midjourney
  prompt: "rotten apple"
categories: dev-setup
tags: [tutorial]
toc:
  title: iMac Reset
  icon: 
socials:
  linkedin: ""
  instagram: ""
  twitter: ""
  facebook: ""
---

A friend got himself a second-hand iMac but couldn't log in
because he didn't have the Apple Account credentials.

![Me with computer science degree as Jessica Jones and relative looking at me to fix their computer as Killgrave meme](/assets/blog-images/reset-mac-3.png){: .img-responsive}

<!--more-->

Create them an account at [account.apple.com](https://account.apple.com),
the shit already starts there, get ready to confirm the email once and
your friends' phone like, literally, a gazillion times.

# The Steps

- Turn on the iMac AND
- Press and hold `Command + R` until it boots into Recovery Mode
- Reinstall macOS

If that worked great! But it will probably fail with a 403 Forbidden
when trying to download the OS.

- Turn off
- Turn on AND
- Press and hold `Command + S` until it boots into a terminal

```sh
mount -uw /
rm /var/db/.applesetupdone
shutdown -h now
```

# Reboot

At this point, you're almost there!

You'll probably get stuck when trying to accept the Terms & Conditions:

- Go back
- Do NOT login into your iCloud Apple Account
- Accept the Terms & Conditions

Once in the OS, you can go to System > iCloud to login the Apple Account.  
At that point you can also kill the old user.

# Conclusion

Best not to tell anyone you are in IT 😃

![Relatives asking if this is free tech support for live once I got my computer science degree meme](/assets/blog-images/reset-mac-2.jpg)

More memes!!

![Kick-Ass movie meme: daughter asks can you fix my computern, Nicolas Cage shoots her in the next panel](/assets/blog-images/reset-imac-1.jpg)
