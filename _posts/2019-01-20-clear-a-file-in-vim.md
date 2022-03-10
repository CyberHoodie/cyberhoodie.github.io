---
layout: post
title:  "Clear a file in Vim"
date:   2019-01-20 12:00:00 -0500
tags: ["Today I Learned", "Vim"]
---

**What I wanted to do:**
Delete all the lines in a file on a remote DigitalOcean server.

**How I did it (thank you [Ashwin](https://codeyarns.com/tech/2011-04-18-how-to-delete-all-lines-of-file-in-vim.html)):**
Opened the file in Vim and performed the following tasks:

```
gg #to get to the top of the file (I had been exploring a bit)
dG #to delete all the lines
```

My daily text editor of choice is Atom, but I used to use Vim quite frequently. I had originally picked it up because it seemed cool. But it eventually was dropped because I never took the time to properly learn it, and crunch time for a project was upon me.

Then I forgot about Vim.

This seemingly boring situation above once again peaked my interest and I'm going to dedicate this week to start my Vim learning path again.
